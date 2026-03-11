# LeetCode 80: Remove Duplicates from Sorted Array II

This is a classic algorithmic problem that tests your ability to manipulate arrays in place.

Here is a clear breakdown of how to solve **LeetCode 80. Remove Duplicates from Sorted Array II** using Golang.

## **Understanding the Goal**

You are given an array of numbers that is already sorted. Your task is to modify the array so that **each number appears a maximum of two times**.

* You must do this **in-place** (without creating a new array).
* You must return the new length of the valid part of the array (let's call this `k`).
* The elements after the first `k` elements do not matter.

## **The Strategy: The Two-Pointer Approach**

Because the array is already sorted, any duplicate numbers will be sitting right next to each other. We can solve this efficiently using two pointers:

1. **The "Fast" Pointer (`i`)**: This goes through every single item in the array to check it.
2. **The "Slow" Pointer (`k`)**: This keeps track of the position where the next valid number should be placed. It also represents the length of our new valid array.

**The Core Logic:**
We can automatically keep the first two elements of the array because a number is allowed to appear up to two times.

For the third element and beyond (starting at index 2), we compare the current number we are checking (`nums[i]`) with the number we placed two steps back (`nums[k-2]`).

* **If they are the same:** It means we already have two copies of this number. We skip it.
* **If they are different:** It means it is safe to add this number. We place it at `nums[k]` and move our slow pointer forward.

## **Golang Implementation**

Here is the clean and efficient Go code for this logic:

```go
func removeDuplicates(nums []int) int {
    // If the array has 2 or fewer elements, it is already valid.
    if len(nums) <= 2 {
        return len(nums)
    }

    // Initialize the slow pointer at index 2.
    // The first two elements (index 0 and 1) are always kept.
    k := 2 

    // The fast pointer 'i' also starts at index 2.
    for i := 2; i < len(nums); i++ {
        // Check if the current element is different from the element 
        // two positions behind our insertion point.
        if nums[i] != nums[k-2] {
            // It's safe to add! Place it at the 'k' position.
            nums[k] = nums[i]
            // Move the insertion point forward.
            k++
        }
    }
    
    // 'k' is now the length of the array without extra duplicates.
    return k
}

```

## **Example Walkthrough**

Let's trace the code with an example array: `nums = [1, 1, 1, 2, 2, 3]`

1. `len(nums)` is 6, so we continue.
2. `k = 2`. The first two elements `[1, 1]` are kept.
3. **i = 2 (value: 1):** We check `nums[i] != nums[k-2]` $\rightarrow$ `1 != nums[0]` $\rightarrow$ `1 != 1`. This is **false**. We skip it.
4. **i = 3 (value: 2):** We check `nums[i] != nums[k-2]` $\rightarrow$ `2 != nums[0]` $\rightarrow$ `2 != 1`. This is **true**.
    * We do `nums[k] = nums[i]` $\rightarrow$ `nums[2] = 2`. Array is now `[1, 1, 2, ...]`.
    * `k` becomes 3.

5. **i = 4 (value: 2):** We check `nums[i] != nums[k-2]` $\rightarrow$ `2 != nums[1]` $\rightarrow$ `2 != 1`. This is **true**.
    * We do `nums[k] = nums[i]` $\rightarrow$ `nums[3] = 2`. Array is now `[1, 1, 2, 2, ...]`.
    * `k` becomes 4.

6. **i = 5 (value: 3):** We check `nums[i] != nums[k-2]` $\rightarrow$ `3 != nums[2]` $\rightarrow$ `3 != 2`. This is **true**.
    * We do `nums[k] = nums[i]` $\rightarrow$ `nums[4] = 3`. Array is now `[1, 1, 2, 2, 3, 3]`.
    * `k` becomes 5.

> **Result:** The function returns `k = 5`. The first 5 elements of the array are exactly what we want: `[1, 1, 2, 2, 3]`.

## **Complexity Analysis**

* **Time Complexity:** O(n) — We loop through the array exactly one time.
* **Space Complexity:** O(1) — We modify the array in place and only use a few integer variables (`k` and `i`), requiring no extra memory.
