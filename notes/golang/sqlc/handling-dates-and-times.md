# Handling Dates and Times in Go with sqlc and pgx/v5

When building applications using Go, sqlc, and pgx/v5, handling dates and times is a critical architectural decision. Mismanagement of time types can lead to timezone bugs, serialization issues, and difficulties in maintaining type safety across the stack.

This article outlines the best practices for configuring this specific stack to achieve type safety, timezone consistency, and developer ergonomics.

## 1. Database Schema: Prefer `TIMESTAMPTZ`

PostgreSQL offers two primary timestamp data types: `TIMESTAMP WITHOUT TIME ZONE` and `TIMESTAMP WITH TIME ZONE` (`TIMESTAMPTZ`).

For most use cases involving events, logs, audit trails, or `created_at`/`updated_at` columns, **`TIMESTAMPTZ` is the recommended choice.**

### Why `TIMESTAMPTZ`?

Contrary to its name, `TIMESTAMPTZ` does not store the timezone offset alongside the data. Instead, PostgreSQL converts the input value to UTC and stores it. When querying, the database converts the UTC value back to the time zone of the current database connection.

Using `TIMESTAMP` (without time zone) relies on the application logic to imply the context of the time, which can lead to errors in distributed systems or when the database and application servers reside in different regions.

```sql
-- Recommended Schema Pattern
CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    email TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ
);

```

## 2. sqlc Configuration: Type Overrides

By default, depending on the version and configuration, `sqlc` may map PostgreSQL timestamp types to driver-specific structs like `pgtype.Timestamptz` or `sql.NullTime`. While these types are precise, they can be cumbersome to work with in business logic (e.g., they do not natively support methods like `.Add()` or `.After()` without casting, and they often require custom JSON marshalers).

The most ergonomic approach is to configure `sqlc` to map these columns directly to the standard library's `time.Time` struct.

### The `sqlc.yaml` Configuration

In your `sqlc.yaml` file, use the `overrides` feature to force the generation of `time.Time` for date and timestamp columns.

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
          - db_type: "pg_catalog.timestamptz"
            go_type: "time.Time"
          - db_type: "pg_catalog.timestamp"
            go_type: "time.Time"
          - db_type: "pg_catalog.date"
            go_type: "time.Time"

```

## 3. Handling Nullability

One of the significant advantages of using the override configuration above is how `sqlc` handles nullable columns.

* **NOT NULL Columns:** Mapped to `time.Time`.
* **NULL Columns:** Mapped to `*time.Time` (a pointer).

Using `*time.Time` is generally preferred over `sql.NullTime` for API development. A pointer naturally marshals to JSON as either the ISO 8601 string (if present) or `null` (if nil), whereas `sql.NullTime` marshals to a struct object (e.g., `{"Time": "...", "Valid": true}`), which is rarely the desired output for a JSON API.

## 4. Application Logic and Precision

Once the types are configured, application logic should adhere to specific standards to ensure data integrity.

### Always Use UTC

Even with `TIMESTAMPTZ` handling storage in UTC, best practice dictates that the application should explicitly generate times in UTC. This avoids reliance on the server's local system clock configuration.

```go
// Recommended
createdAt := time.Now().UTC()

```

### Managing Precision (Nanoseconds vs. Microseconds)

PostgreSQL supports microsecond precision (6 decimal places), whereas Go's `time.Time` supports nanosecond precision.

If you generate a time in Go, save it to the database, and read it back, the values will not be identical because the database will truncate the nanoseconds. This often causes unit tests using `reflect.DeepEqual` to fail.

To handle this, truncate times used in strictly equal comparisons or tests:

```go
// Truncate to match Postgres precision
now := time.Now().UTC().Truncate(time.Microsecond)

```

## 5. Driver Configuration (pgx)

While `pgx` handles the binary protocol efficiently, setting the connection session time zone to UTC is a defensive measure. This ensures that any logic relying on the session time zone (such as complex SQL queries involving date math) behaves consistently.

```go
config, err := pgxpool.ParseConfig(databaseURL)
if err != nil {
    log.Fatal(err)
}

config.AfterConnect = func(ctx context.Context, conn *pgx.Conn) error {
    // Force the session to UTC
    _, err := conn.Exec(ctx, "SET TIME ZONE 'UTC'")
    return err
}

```

## Summary

To create a robust time-handling architecture in Go with sqlc and pgx:

1. **Database:** Use `TIMESTAMPTZ` for all point-in-time data.
2. **Configuration:** Override `timestamptz` to `time.Time` in `sqlc.yaml`.
3. **Code:** Use `*time.Time` for nullable fields to ensure clean JSON serialization.
4. **Consistency:** Always generate times using `time.Now().UTC()` and be aware of microsecond truncation.
