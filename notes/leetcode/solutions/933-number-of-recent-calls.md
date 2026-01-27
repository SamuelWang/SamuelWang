# LeetCode 933: Number of Recent Calls in Go

The "Number of Recent Calls" problem (LeetCode 933) is a classic algorithmic challenge that simulates a basic rate-limiting mechanism or sliding window analytics system. The core task is to count how many requests have occurred within the last 3000 milliseconds relative to a current timestamp.

This article explores the solution using **Go (Golang)**, analyzing the architectural pattern, implementation details, and performance characteristics.

## 1. Problem Analysis

The problem requires the implementation of a `RecentCounter` class with a single method: `Ping(t)`.

* **Input:** A timestamp `t` (in milliseconds). It is guaranteed that `t` strictly increases with every call.
* **Output:** The number of requests in the inclusive range `[t - 3000, t]`.

#### The Architectural Pattern: Sliding Window

Since the timestamps are guaranteed to be chronological (strictly increasing), the problem can be modeled as a **Sliding Window** or a **First-In-First-Out (FIFO) Queue**.

1. **Enqueue:** When a new ping arrives at time `t`, it is added to the end of the data stream.
2. **Dequeue:** As the time window moves forward to `t`, the lower bound of the window becomes `t - 3000`. Any timestamp strictly smaller than this lower bound is "expired" and can be removed from the front of the stream.
3. **Count:** After removing expired elements, the remaining count of elements represents the number of recent calls.

## 2. Go Implementation

In Go, a slice is the most idiomatic way to implement a queue for this specific problem. Slices support dynamic resizing and efficient indexing.

```go
package main

type RecentCounter struct {
    // requests acts as a queue to store timestamps.
    requests []int
}

// Constructor initializes the RecentCounter.
func Constructor() RecentCounter {
    return RecentCounter{
        requests: make([]int, 0),
    }
}

// Ping adds the new timestamp t and returns the count of requests 
// within the time frame [t-3000, t].
func (this *RecentCounter) Ping(t int) int {
    // 1. Append the current timestamp to the queue.
    this.requests = append(this.requests, t)
    
    // 2. Define the lower bound of the time window.
    limit := t - 3000
    
    // 3. Remove timestamps that are older than the limit.
    // We check the first element (front of the queue). 
    // If it is smaller than the limit, it is expired.
    for len(this.requests) > 0 && this.requests[0] < limit {
        // Slice off the first element.
        this.requests = this.requests[1:]
    }
    
    // 4. The length of the slice is the number of valid recent calls.
    return len(this.requests)
}

```

## 3. Complexity Analysis

Efficiency is critical for stream processing. While the `for` loop inside the `Ping` function might suggest linear time complexity, the amortized analysis proves otherwise.

#### Time Complexity: Amortized $`O(1)`$

* Each timestamp is added to the queue exactly once.
* Each timestamp is removed from the queue exactly once.
* Over a sequence of $`N`$ calls, there are roughly $`2N`$ operations (N insertions + N deletions).
* Therefore, the average time per `Ping` operation is constant, or $`O(1)`$.

#### Space Complexity: $`O(W)`$

* The space required depends on the window size. Let $`W`$ be the maximum number of requests that can physically occur within a 3000ms timeframe.
* The queue will never store more than $`W`$ elements at any given time.
* If the request rate is low, space consumption is negligible.

## 4. Go-Specific Considerations

When implementing this solution in Go, two specific language mechanics are relevant:

**1. Slice Mechanics and Memory Usage**
The operation `this.requests = this.requests[1:]` is efficient because it simply advances the pointer of the slice header. However, it does not immediately free the underlying array's memory at the beginning of the buffer.

* In a short-lived LeetCode execution, this is irrelevant.
* In a long-running production service, if the underlying array grows large during a traffic spike and then is sliced down, the backing array may remain large. Systems might need to occasionally copy the active elements to a new, smaller slice to reclaim memory (compaction).

**2. Concurrency Safety**
The standard Go slice is **not thread-safe**. If this `RecentCounter` were used in a web server where multiple requests call `Ping` concurrently, race conditions would occur.

* **Production Solution:** To make this thread-safe, the struct should include a `sync.Mutex` or `sync.RWMutex`, locking before accessing the slice and unlocking before returning.
