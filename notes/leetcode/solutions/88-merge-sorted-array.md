# LeetCode 88: Merge Sorted Array

Here is a clear breakdown of LeetCode 88: Merge Sorted Array, along with an efficient solution in Go.

## The Problem in Simple Terms

You are given two arrays of numbers, `nums1` and `nums2`, which are already sorted from smallest to largest. Your goal is to combine them into a single sorted array.

The catch is that you must do this **in-place** inside `nums1`. To make this possible, `nums1` is made large enough to hold both arrays. It has a total length of `m + n`, where:

* `m` is the number of actual values in `nums1`.
* `n` is the number of values in `nums2`.
* The last `n` spaces in `nums1` are filled with `0`s (these are just empty placeholders).

## The Strategy: Three Pointers from the Back

If you try to merge from the front, you will accidentally overwrite the numbers in `nums1` before you have a chance to sort them. To fix this, the best approach is to start from the **end** of the arrays and work backward.

We use three pointers:

1. **`i`**: Points to the last actual number in `nums1` (at index `m - 1`).
2. **`j`**: Points to the last number in `nums2` (at index `n - 1`).
3. **`k`**: Points to the very last empty slot in `nums1` (at index `m + n - 1`).

We compare the numbers at `i` and `j`. The larger number gets placed at position `k`. Then, we move the pointers backward and repeat until all numbers from `nums2` are placed.

## Golang Solution

Here is the code using this approach:

```go
func merge(nums1 []int, m int, nums2 []int, n int) {
    // Initialize the three pointers
    i := m - 1
    j := n - 1
    k := m + n - 1

    // Compare elements from the back and place the largest at k
    for i >= 0 && j >= 0 {
        if nums1[i] > nums2[j] {
            nums1[k] = nums1[i]
            i--
        } else {
            nums1[k] = nums2[j]
            j--
        }
        k--
    }

    // If there are still elements left in nums2, copy them over.
    // (If there are elements left in nums1, they are already in the correct place)
    for j >= 0 {
        nums1[k] = nums2[j]
        j--
        k--
    }
}

```

## Complexity Analysis

* **Time Complexity:** `O(m + n)`
We only loop through the elements in `nums1` and `nums2` exactly once.
* **Space Complexity:** `O(1)`
We modify `nums1` directly without creating any new arrays or using extra memory.
