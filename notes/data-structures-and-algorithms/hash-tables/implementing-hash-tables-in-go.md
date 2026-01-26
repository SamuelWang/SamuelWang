# Understanding and Implementing Hash Tables in Golang

The Hash Table (or Hash Map) is one of the most critical data structures in computer science. It allows for the storage of key-value pairs with the goal of achieving $`O(1)`$ average-time complexity for lookups, insertions, and deletions.

In Go, this is implemented natively as the `map` type. However, understanding how to construct a hash table from scratch is essential for grasping the underlying mechanics of memory layout, collision resolution, and concurrency in distributed systems.

## 1. Architectural Overview

At its core, a hash table maps keys to values by calculating a numerical index (hash) from the key. A robust implementation relies on three pillars:

1. **Hash Function:** A process that deterministically converts a key (e.g., a string or integer) into a hash code (an integer). Ideally, this function distributes keys uniformly across the available buckets.
2. **Bucket Array:** A contiguous slice of memory where data is actually stored.
3. **Collision Resolution:** A strategy to handle cases where two different keys produce the same index (hash collision).

### The Native Go Implementation

Go's built-in `map` is highly optimized. It uses a structure called `hmap` in the runtime.

* **Bucketing:** It uses buckets that hold up to 8 key/value pairs directly to optimize for CPU cache locality.
* **Overflow:** If a bucket overflows, it links to an "overflow bucket" (a variation of chaining).
* **Safety:** The native map is **not** thread-safe for concurrent read/writes.

## 2. Design Choices for Custom Implementation

When building a custom hash table, particularly one intended for concurrent use (like a cache), specific design patterns are required.

### Chaining vs. Open Addressing

There are two main ways to handle collisions:

* **Separate Chaining:** Each bucket contains a linked list or dynamic slice of entries. If a collision occurs, the new item is appended to the list.
* **Open Addressing:** All entries are stored in the bucket array itself. If a collision occurs, the algorithm probes for the next empty slot.

**Choice:** This article demonstrates **Separate Chaining** using slices. It is generally simpler to implement and handles high load factors (more items than buckets) more gracefully than open addressing.

### Concurrency: Sharding vs. Global Locking

To make a hash table thread-safe, one approach is to wrap the entire structure in a `sync.Mutex`. However, this creates a bottleneck where only one goroutine can write at a time.

**Choice:** This implementation uses **Sharding**. The hash table is divided into distinct segments (shards), each with its own lock. This significantly reduces lock contention, as multiple goroutines can write simultaneously provided they are accessing different shards.

## 3. Implementation in Go

This implementation uses **Go Generics** (introduced in Go 1.18) to allow the hash table to handle any comparable key type and any value type.

### The Structures

We define a `HashTable` that holds a slice of `Shard` pointers. Each `Shard` contains a read/write mutex and a slice of items.

```go
package main

import (
	"fmt"
	"hash/fnv"
	"sync"
)

// ShardCount determines the level of concurrency.
// In a production system, this is often a power of 2 to allow bitwise masking.
const ShardCount = 64

// KV represents the data node containing the key and value.
type KV[K comparable, V any] struct {
	Key   K
	Value V
}

// Shard represents a distinct segment of the map with its own lock.
type Shard[K comparable, V any] struct {
	sync.RWMutex // Read/Write Mutex for concurrent access
	items        []KV[K, V]
}

// HashTable represents the high-level structure containing all shards.
type HashTable[K comparable, V any] struct {
	shards []*Shard[K, V]
}

// NewHashTable initializes the system with empty shards.
func NewHashTable[K comparable, V any]() *HashTable[K, V] {
	ht := &HashTable[K, V]{
		shards: make([]*Shard[K, V], ShardCount),
	}
	for i := 0; i < ShardCount; i++ {
		ht.shards[i] = &Shard[K, V]{
			items: make([]KV[K, V], 0),
		}
	}
	return ht
}

```

### The Hashing Strategy

We need a method to route a key to a specific shard. This example uses FNV-1a for simplicity. In production, cryptographically secure hashes or algorithms resistant to Hash DoS (like SipHash) are preferred.

```go
func (ht *HashTable[K, V]) getShardIndex(key K) int {
	h := fnv.New32a()
	// Simple serialization for demonstration. 
	// Production code would use type-specific hashing for performance.
	h.Write([]byte(fmt.Sprintf("%v", key))) 
	return int(h.Sum32()) % ShardCount
}

```

### Operations: Insert, Get, and Remove

These operations utilize the `RWMutex`.

* **Put (Insert):** Requires a Write Lock to ensure exclusive access to the shard during modification.
* **Get (Retrieve):** Requires a Read Lock, allowing multiple concurrent readers.

```go
// Put inserts or updates a value associated with a key.
func (ht *HashTable[K, V]) Put(key K, value V) {
	index := ht.getShardIndex(key)
	shard := ht.shards[index]

	shard.Lock() // Acquire Write Lock
	defer shard.Unlock()

	// Check if key exists and update it
	for i, kv := range shard.items {
		if kv.Key == key {
			shard.items[i].Value = value
			return
		}
	}

	// Collision resolution: Append to the slice (Chaining)
	shard.items = append(shard.items, KV[K, V]{Key: key, Value: value})
}

// Get retrieves a value by key. Returns the value and a boolean existence flag.
func (ht *HashTable[K, V]) Get(key K) (V, bool) {
	index := ht.getShardIndex(key)
	shard := ht.shards[index]

	shard.RLock() // Acquire Read Lock
	defer shard.RUnlock()

	for _, kv := range shard.items {
		if kv.Key == key {
			return kv.Value, true
		}
	}

	var zero V
	return zero, false
}

// Remove deletes a key from the map.
func (ht *HashTable[K, V]) Remove(key K) {
	index := ht.getShardIndex(key)
	shard := ht.shards[index]

	shard.Lock()
	defer shard.Unlock()

	for i, kv := range shard.items {
		if kv.Key == key {
			// Delete by swapping with the last element.
			// This is efficient O(1) for unordered slices as it avoids shifting elements.
			lastIdx := len(shard.items) - 1
			shard.items[i] = shard.items[lastIdx]
			shard.items = shard.items[:lastIdx]
			return
		}
	}
}

```

## 4. Analysis and Performance

### Complexity

* **Time Complexity:**
  * **Average Case:** $`O(1)`$. This assumes the hash function distributes keys evenly across shards.
  * **Worst Case:** $`O(n)`$. If a poor hash function sends all keys to the same shard, the shard degrades into a linear list, requiring a full scan for every lookup.

* **Space Complexity:** $`O(n)`$, where  is the number of stored elements.

### Limitations of this Implementation

1. **No Dynamic Resizing:**
The example above uses a fixed number of shards. If millions of items are added, the slices within the shards will grow large, and performance will degrade to $`O(n)`$. A production-ready hash table tracks the **Load Factor** and expands (rehashes) the table when the number of elements exceeds a certain threshold relative to the number of buckets.
2. **Memory Overhead:**
While using slices is cache-friendly, the overhead of the `sync.RWMutex` on every shard adds up. However, this is a necessary trade-off for fine-grained concurrency control.

### Summary

Implementing a hash table in Go provides insight into data structure alignment and concurrency patterns. By using sharding, developers can create thread-safe caches that minimize lock contention, offering a significant performance advantage over a single global lock in high-throughput environments.
