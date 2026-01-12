# LeetCode 167: Two Sum II - Input Array Is Sorted

In the landscape of algorithmic interviews and systems optimization, **LeetCode 167: Two Sum II - Input Array Is Sorted** is a classic study in how data structure properties—specifically strict ordering—dictate our algorithm choice.

Unlike the standard "Two Sum" where we trade space for time using a Hash Map, this variation constrains us to **constant extra space** ($O(1)$) but gives us a powerful asset: the input array is **sorted**.

Here is the architectural breakdown and the Go implementation.

## 1. Requirements Analysis

**The Goal:** Find two numbers in `numbers` (1-indexed) that add up to a specific `target`.

**Constraints & Invariants:**

1. **Sorted Input:** The array is sorted in non-decreasing order. This is the critical architectural driver.
2. **Space Constraint:** We must use $O(1)$ extra space. This rules out Hash Maps (which cost $O(n)$ space).
3. **Determinism:** There is exactly one solution.

## 2. Algorithmic Strategy: Two Pointers

When dealing with sorted arrays and search problems involving pairs, the **Two Pointer** technique is often the optimal pattern. It allows us to narrow the search space linearly rather than quadratically.

**The Logic:**
Imagine two pointers:

* `left` pointing to the smallest number (start of array).
* `right` pointing to the largest number (end of array).

We calculate the `currentSum = numbers[left] + numbers[right]`.

* **Case A (`currentSum == target`):** Success. We found our pair. Return `[left+1, right+1]`.
* **Case B (`currentSum > target`):** The sum is too large. Since `numbers` is sorted, the only way to reduce the sum is to move to a smaller number. We must decrement the `right` pointer.
* **Case C (`currentSum < target`):** The sum is too small. The only way to increase the sum is to pick a larger number. We must increment the `left` pointer.

This approach guarantees we check the necessary pairs in $O(n)$ time complexity, effectively pruning invalid pairs without calculating them.

## 3. Implementation (Golang)

Here is the idiomatic Go solution. Note the clean loop structure and adherence to the 1-based indexing requirement.

```go
package main

// twoSum finds the indices of the two numbers that add up to target.
// Input: numbers (sorted ascending), target
// Output: []int{index1, index2} (1-based indices)
func twoSum(numbers []int, target int) []int {
    // Initialize pointers
    left := 0
    right := len(numbers) - 1

    for left < right {
        sum := numbers[left] + numbers[right]

        if sum == target {
            // Requirement asks for 1-based indexing
            return []int{left + 1, right + 1}
        } else if sum < target {
            // Sum is too small; we need a larger value.
            // Move the left pointer to the right.
            left++
        } else {
            // Sum is too large; we need a smaller value.
            // Move the right pointer to the left.
            right--
        }
    }

    // Since the problem guarantees exactly one solution,
    // technically this part is unreachable, but good practice requires a return.
    return []int{-1, -1}
}

```

## 4. Technical Analysis & Trade-offs

| Metric | Complexity | Explanation |
| --- | --- | --- |
| **Time Complexity** | $O(n)$ | In the worst case, the pointers meet somewhere in the middle. We touch each element at most once. |
| **Space Complexity** | $O(1)$ | We only store two integer variables (`left` and `right`), regardless of input size. |

**Why not Binary Search?**
You *could* iterate through the array and perform a binary search for the complement (`target - current_val`) for each element.

* **Time:** $O(n \log n)$
* **Space:** $O(1)$
* **Verdict:** This is acceptable but sub-optimal compared to the $O(n)$ Two Pointer approach.

**Why not a Hash Map (Standard Two Sum)?**

* **Time:** $O(n)$
* **Space:** $O(n)$
* **Verdict:** This violates the constant space constraint required by the problem statement.

### 5. Principal Engineer Takeaway

> **"Read the constraints before you choose the data structure."**

In system design and algorithm challenges, constraints (like "sorted" or "limited memory") are not just details; they are hints pointing toward the optimal solution. In production, using a Hash Map here would waste memory. Using Brute Force ($O(n^2)$) would waste CPU. The Two Pointer approach respects the data's natural ordering to achieve maximum efficiency.
