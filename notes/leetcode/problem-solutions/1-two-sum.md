# LeetCode 1: Two Sum

Let's break down LeetCode Problem #1, **Two Sum**, using Go. This is a foundational problem that tests your ability to use data structures effectively to optimize performance.

## The Problem Statement

Given an array of integers `nums` and an integer `target`, return the indices of the two numbers such that they add up to the `target`.

* **Constraint:** You may assume that each input would have **exactly one solution**, and you may not use the same element twice.
* **Goal:** Return the answer in any order.

## Approach 1: The Brute Force Method (Naïve)

In a code review, if I saw a junior engineer implement this, I would note it as a "functional baseline" but flag it for immediate refactoring if the dataset size ($`n`$) is expected to grow.

**The Logic:**
We iterate through the array with pointer `i`, and for every `i`, we iterate through the rest of the array with pointer `j` to see if `nums[i] + nums[j] == target`.

```go
func twoSumBruteForce(nums []int, target int) []int {
    // Iterate through the first number
    for i := 0; i < len(nums); i++ {
        // Iterate through the second number
        for j := i + 1; j < len(nums); j++ {
            if nums[i] + nums[j] == target {
                return []int{i, j}
            }
        }
    }
    return nil // Should not be reached based on problem constraints
}
```

**Architectural Analysis:**

* **Time Complexity:** $$O(n^2)$$

  - For every element, we scan the rest of the array. As input size doubles, execution time quadruples.
  
* **Space Complexity:** $$O(1)$$

  - We aren't allocating extra memory structure.
 
* **Verdict:** Acceptable only for very small, fixed datasets. Unacceptable for scalable systems.

## Approach 2: The Hash Map Optimization (Preferred)

This is the industry-standard approach. We trade **memory** for **speed**. Instead of scanning for the second number repeatedly, we memorize the numbers we've seen so far to allow for $$O(1)$$ lookups.

**The Logic:**

1. Initialize a hash map (in Go, a `map`). Key = the number value, Value = the index.
2. Iterate through the array once.
3. For each current number `x`, calculate the `complement` needed to reach the target:  
  $$complement = target - x$$

4. Check if the `complement` exists in our map.

    * **If yes:** We found the pair! Return the stored index and the current index.
    * **If no:** Store the current number and its index in the map and continue.
    * 
**The Solution in Go:**

```go
func twoSum(nums []int, target int) []int {
    // Create a map to store the value and its index.
    // Optimization: If we know the size roughly, we could pre-allocate,
    // but a standard map init is fine here.
    seen := make(map[int]int)

    for i, num := range nums {
        complement := target - num

        // Check if the complement exists in the map
        // The "comma-ok" idiom in Go is idiomatic for checking map existence
        if idx, ok := seen[complement]; ok {
            return []int{idx, i}
        }

        // Store current number and index for future lookups
        seen[num] = i
    }

    return nil
}

```

**Architectural Analysis:**

* **Time Complexity:** $$O(n)$$

  - We traverse the list containing  elements exactly once. Map lookups are amortized $$O(1)$$.

* **Space Complexity:** $$O(n)$$

  - In the worst case, we store almost every element in the map before finding the pair.

## Why this matters

In a distributed system or a high-throughput microservice, the difference between $$O(n^2)$$ and $$O(n)$$ is the difference between a system that scales and one that causes a cascading failure (timeout) under load.

**Go-Specific Best Practices used here:**

1. **`range` loop:** Clean and idiomatic way to get both index and value.
2. **`comma-ok` idiom:** `if idx, ok := seen[complement]; ok` is the safe, standard way to check for key existence without crashing or getting a zero-value false positive.
3. **Memory Management:** Go's garbage collector handles the map cleanup, but being aware that we are allocating heap memory for the map is important for constrained environments (like embedded systems).

