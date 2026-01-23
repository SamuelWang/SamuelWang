# Handling UUIDs with Go, sqlc, and pgx/v5

The combination of Go, **sqlc** (for type-safe SQL generation), and **pgx/v5** (as the PostgreSQL driver) creates a robust and performant database layer. However, a common friction point in this stack is the handling of UUIDs.

By default, `pgx` utilizes its own `pgtype.UUID` type (essentially a raw byte array). While functional, this type lacks the convenience methods—such as generation, validation, and string parsing—found in the industry-standard `github.com/google/uuid` package.

This article outlines the best practices for bridging these tools to achieve type safety, performance, and developer ergonomics.

## 1. Configuring Type Overrides in sqlc

The most effective strategy is to configure `sqlc` to automatically map the PostgreSQL `uuid` column type to the Go `github.com/google/uuid` type. This eliminates the need for manual conversion logic between the database driver types and business logic types.

To achieve this, modify the `sqlc.yaml` configuration file. You must define an `overrides` block that tells the generator to substitute the default type with the external library.

**Example `sqlc.yaml`:**

```yaml
version: "2"
sql:
  - schema: "schema.sql"
    queries: "query.sql"
    engine: "postgresql"
    gen:
      go:
        package: "db"
        out: "db"
        sql_package: "pgx/v5"
        overrides:
          # Map standard UUID columns to google/uuid
          - db_type: "uuid"
            go_type: "github.com/google/uuid.UUID"
          # Map nullable UUID columns to uuid.NullUUID
          - db_type: "uuid"
            go_type: "github.com/google/uuid.NullUUID"
            nullable: true

```

With this configuration, the generated struct fields will use `uuid.UUID` instead of `pgtype.UUID`.

## 2. Registering Binary Serialization

While the overrides above allow the code to compile, `pgx` needs to know how to efficiently encode and decode the `google/uuid` type using the PostgreSQL binary protocol. Without explicit registration, the driver may fall back to text encoding, which incurs a performance penalty.

To resolve this, use the adapter library `pgx-google-uuid`.

**Installation:**

```bash
go get github.com/vgarvardt/pgx-google-uuid/v5

```

**Connection Pool Configuration:**

The data type must be registered on every connection created by the pool. This is done using the `AfterConnect` hook in the `pgxpool.Config`.

```go
package db

import (
    "context"
    "github.com/jackc/pgx/v5"
    "github.com/jackc/pgx/v5/pgxpool"
    pgxuuid "github.com/vgarvardt/pgx-google-uuid/v5"
)

func NewPool(ctx context.Context, connStr string) (*pgxpool.Pool, error) {
    // Parse the default configuration
    config, err := pgxpool.ParseConfig(connStr)
    if err != nil {
        return nil, err
    }

    // Register the UUID type map for every new connection
    config.AfterConnect = func(ctx context.Context, conn *pgx.Conn) error {
        pgxuuid.Register(conn.TypeMap())
        return nil
    }

    return pgxpool.NewWithConfig(ctx, config)
}

```

## 3. Handling Nullable UUIDs

When dealing with columns that allow `NULL` (e.g., `manager_id uuid`), developers must decide how to represent the "no value" state in Go. There are two primary approaches available via `sqlc` overrides.

### Option A: Use `uuid.NullUUID` (Recommended for Internal Safety)

This uses a struct `{UUID: ..., Valid: bool}`.

* **Pros:** Explicit validity check prevents `nil` pointer panics.
* **Cons:** Default JSON marshaling produces a nested object structure, which may not align with API contracts expecting `null`.

### Option B: Use Pointers `*uuid.UUID` (Recommended for JSON APIs)

To use pointers, adjust the override configuration to include `pointer: true`.

```yaml
- db_type: "uuid"
  go_type: "github.com/google/uuid.UUID"
  pointer: true
  nullable: true

```

* **Pros:** Marshals directly to `null` in JSON.
* **Cons:** Requires nil-checking to avoid runtime panics.

## 4. ID Generation Strategy

A common pitfall arises when relying on database defaults (e.g., `DEFAULT gen_random_uuid()`) alongside Go's zero values.

The zero value of `uuid.UUID` is `00000000-0000-0000-0000-000000000000`. If a struct with a zero-value ID is passed to an `INSERT` query, `pgx` will insert that specific zero-UUID rather than triggering the database's default generation logic (which usually triggers only on `NULL` or omitted columns).

**Best Practice:**
Generate the UUID in the application layer immediately before persistence.

```go
// Preferred: Deterministic and explicit
params := db.CreateUserParams{
    ID:   uuid.New(), 
    Name: "Alice",
}

```

This approach ensures the ID is known immediately without needing a `RETURNING` clause and prevents accidental zero-UUID insertions.
