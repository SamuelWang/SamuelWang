# LeetCode: 217. Contains Duplicate

The "Contains Duplicate" problem (LeetCode 217) is a fundamental algorithmic challenge that serves as an excellent case study for understanding data structure trade-offs, specifically between time complexity and memory usage.

The problem asks: Given an integer array `nums`, return `true` if any value appears at least twice in the array, and return `false` if every element is distinct.

Below is a detailed analysis of the two primary approaches to solving this problem in Go: the **Hash Map** approach (optimized for speed) and the **Sorting** approach (optimized for space).

## Approach 1: Hash Map (Time Optimized)

The standard and most efficient way to solve this problem in production environments is using a Hash Map (or Hash Set). This allows for checking the existence of an element in constant time, $`O(1)`$, on average.

### Golang Optimization: Empty Structs

In many languages, a `Set` data structure is used. Go does not have a built-in `Set` type, so developers often use `map[int]bool`. However, a `bool` value occupies 1 byte of memory.

To optimize for memory, Go developers often use `map[int]struct{}`. An empty struct (`struct{}`) occupies **0 bytes** of storage. This signifies that we are only interested in the existence of the key, not the value associated with it.

### Implementation

```go
func containsDuplicate(nums []int) bool {
    // Edge case check
    if len(nums) <= 1 {
        return false
    }

    // Initialize map
    // Optimization: Pre-allocate capacity to avoid map resizing overhead
    seen := make(map[int]struct{}, len(nums))

    for _, num := range nums {
        // Check if the number already exists in the map
        if _, exists := seen[num]; exists {
            return true
        }
        // Store the key with an empty struct (0 bytes)
        seen[num] = struct{}{}
    }

    return false
}

```

### Complexity Analysis

* **Time Complexity:** $`O(N)`$. We iterate through the array once.
* **Space Complexity:** $`O(N)`$. In the worst case (all elements are unique), the map stores every element.

## Approach 2: Sorting (Space Optimized)

If the system is constrained by memory (e.g., embedded systems) or if the input array is extremely large and allocating a map would cause an Out of Memory (OOM) error, sorting is a viable alternative.

By sorting the array first, all duplicate values become adjacent. The algorithm then iterates through the sorted array to check if `nums[i] == nums[i+1]`.

### Implementation

```go
import "sort"

func containsDuplicateSorted(nums []int) bool {
    // Sort the slice
    // Note: This mutates the original slice.
    sort.Ints(nums)

    // Iterate and check adjacent elements
    for i := 0; i < len(nums)-1; i++ {
        if nums[i] == nums[i+1] {
            return true
        }
    }

    return false
}

```

### Complexity Analysis

* **Time Complexity:** $`O(N \log N)`$. The dominant operation is the sorting algorithm (Go typically uses a variation of Quicksort/PDQsort).
* **Space Complexity:** $`O(1)`$ or $`O(\log N)`$. This depends on the stack space required by the sorting implementation, but it avoids allocating heap memory proportional to the input size.

## Comparative Analysis

When choosing between these two solutions, the decision rests on the specific constraints of the environment.

| Feature | Hash Map (Approach 1) | Sorting (Approach 2) |
| --- | --- | --- |
| **Primary Goal** | **Speed.** Ideal for high-throughput APIs. | **Memory Efficiency.** Ideal for constrained environments. |
| **Time Complexity** | $`O(N)`$ | $`O(N \log N)`$ |
| **Space Complexity** | $`O(N)`$ | $`O(1)`$ / $`O(\log N)`$ |
| **Data Integrity** | Preserves original array order. | **Mutates input.** The array is reordered. |

### Production Considerations

1. **Map Capacity Pre-allocation:** When using the Hash Map approach, using `make(map[T]struct{}, size)` is crucial for performance. Without a size hint, the Go runtime must dynamically resize the map and re-hash keys as elements are added, which incurs significant CPU overhead.
2. **Immutability:** The sorting approach modifies the input slice. In a functional programming context or when the input data is reused elsewhere, this side effect is undesirable. If the original data must be preserved, a copy must be made before sorting, which negates the space savings.
3. **Concurrency:** Neither approach is inherently thread-safe. If the `nums` slice is being modified by another goroutine during execution, race conditions will occur.

### Conclusion

For general-purpose software engineering where memory is not the primary bottleneck, **Approach 1 (Hash Map)** is preferred due to its linear time complexity. The use of `map[int]struct{}` demonstrates idiomatic Go usage by minimizing memory footprint. Approach 2 is best reserved for specific edge cases where memory allocation is strictly limited.
