# LeetCode 283: Move Zeroes

Hello. As a Software Development Expert, I am happy to break down **LeetCode Problem 283: Move Zeroes**.

This is a classic algorithmic problem that tests your ability to manipulate arrays (or slices in Go) **in-place**. In real-world software engineering, this pattern is useful when data locality matters and you need to optimize memory usage by avoiding unnecessary allocations.

## 1. Requirement Analysis & Constraints

Before writing code, we must understand the specific constraints of this problem:

1. **In-Place:** You must modify the `nums` array directly. You cannot allocate a new array (which would make the space complexity $`O(n)`$).
2. **Relative Order:** The order of the non-zero elements must remain unchanged. (e.g., if `1` comes before `3`, it must stay that way).
3. **Minimize Operations:** We want to achieve this in linear time $`O(n)`$.

## 2. The Solution: Two-Pointer Approach

The most efficient pattern here is the **Two-Pointer Technique** (specifically, a "Reader/Writer" pattern).

### The Concept

Imagine you have two pointers (indices):

1. **`right` (Reader):** Scans the array from start to finish.
2. **`left` (Writer):** Keeps track of the position where the *next non-zero* element should be placed.

**The Logic:**

* Iterate through the array with the `right` pointer.
* Whenever `right` encounters a **non-zero** number, we move it to the `left` position.
* Then, we increment `left`.
* If `right` encounters a zero, we do nothing but keep moving `right`.

By the time the `right` pointer finishes, all non-zero numbers are shifted to the front in their original order.

### Why Go is Great for This

Go supports **multiple assignment**, allowing us to swap variables in a single line without a temporary variable:
`a, b = b, a`

This allows us to swap the non-zero element into the correct spot immediately, handling both the shifting of numbers and the placement of zeros in one pass.

## 3. Go Implementation

Here is the clean, idiomatic Go solution.

```go
package main

// moveZeroes moves all 0s to the end of nums while maintaining the 
// relative order of the non-zero elements.
func moveZeroes(nums []int) {
    // 'left' is the index where we will place the next non-zero element found.
    left := 0

    // 'right' iterates through the array looking for non-zero elements.
    for right := 0; right < len(nums); right++ {
        if nums[right] != 0 {
            // Optimization: Only swap if the indices are different.
            // This prevents unnecessary work if the array has no zeros at the start.
            if left != right {
                // Idiomatic Go swap
                nums[left], nums[right] = nums[right], nums[left]
            }
            left++
        }
    }
}

```

### Dry Run / Trace

Input: `[0, 1, 0, 3, 12]`

1. **Start:** `left=0`, `right=0`. `nums[0]` is 0. Do nothing.
2. **Step 2:** `right=1`. `nums[1]` is 1 (non-zero).
* Swap `nums[0]` and `nums[1]`. Array is now `[1, 0, 0, 3, 12]`.
* Increment `left` to 1.


3. **Step 3:** `right=2`. `nums[2]` is 0. Do nothing.
4. **Step 4:** `right=3`. `nums[3]` is 3 (non-zero).
* Swap `nums[1]` and `nums[3]`. Array is now `[1, 3, 0, 0, 12]`.
* Increment `left` to 2.


5. **Step 5:** `right=4`. `nums[4]` is 12 (non-zero).
* Swap `nums[2]` and `nums[4]`. Array is now `[1, 3, 12, 0, 0]`.



**Result:** `[1, 3, 12, 0, 0]` (Correct).

---

## 4. Technical Complexity Analysis

When discussing this with stakeholders or in a code review, you should summarize the performance as follows:

* **Time Complexity:** $`O(n)`$
* We traverse the array exactly once with the `right` pointer.  is the number of elements in the slice.


* **Space Complexity:** $`O(1)`$
* We are performing operations **in-place**. We only use two integer variables (`left` and `right`) for storage, regardless of the input size.



### Corner Cases Considered

* **Empty Slice:** The loop condition `right < len(nums)` handles this gracefully (loop never runs).
* **No Zeros:** The `left` and `right` pointers will move together. The `if left != right` check prevents unnecessary self-swaps.
* **All Zeros:** `left` never increments; the array remains unchanged.
