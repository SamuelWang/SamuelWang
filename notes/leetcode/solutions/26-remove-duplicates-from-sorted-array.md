# LeetCode 26: Remove Duplicates from Sorted Array

If you are practicing for technical interviews or sharpening your algorithmic thinking, LeetCode 26 is a classic problem. It introduces a highly practical technique for array manipulation: the **Two-Pointer** approach.

Here is a complete breakdown of the problem, the core concept, and a clean solution in Golang.

## The Problem

**LeetCode 26: Remove Duplicates from Sorted Array**

Given an integer array `nums` sorted in **non-decreasing order**, remove the duplicates **in-place** such that each unique element appears only once. The relative order of the elements should be kept the same. Then return the number of unique elements in `nums`.

Consider the number of unique elements of `nums` to be `k`. To get your solution accepted, you need to do the following things:

1. Change the array `nums` such that the first `k` elements of `nums` contain the unique elements in the order they were present in `nums` initially. The remaining elements of `nums` are not important as well as the size of `nums`.
2. Return `k`.

**Example:**

* **Input:** `nums = [0,0,1,1,1,2,2,3,3,4]`
* **Output:** `5`, `nums = [0,1,2,3,4,_,_,_,_,_]` (The underscores represent spaces in the array we no longer care about).

## The Approach: Two Pointers

The key constraint here is that the operation must be done **in-place**. You cannot create a second array to hold the unique numbers; you must modify the existing one. Because the array is already sorted, any identical numbers will be grouped directly next to each other.

We can solve this efficiently by using two index variables (pointers) to read and overwrite the array simultaneously:

1. **The Slow Pointer (`slow`):** This tracks the boundary of our "unique" array. It points to the exact position where the *next* discovered unique number should go.
2. **The Fast Pointer (`fast`):** This is the scout. It loops through the entire array looking for numbers we haven't seen yet.

When the `fast` pointer lands on a number that is different from the number currently at the `slow` pointer, it means we found a new unique value. We then move the `slow` pointer up by one step and overwrite that spot with the new value found by the `fast` pointer.

## The Golang Implementation

Here is how you write this logic cleanly in Go:

```go
package main

import "fmt"

func removeDuplicates(nums []int) int {
    // Handle the edge case of an empty array immediately
    if len(nums) == 0 {
        return 0
    }

    // slow pointer starts at index 0
    slow := 0

    // fast pointer scans starting from index 1
    for fast := 1; fast < len(nums); fast++ {
        // When a new unique element is found...
        if nums[fast] != nums[slow] {
            // ...move the slow pointer forward...
            slow++
            // ...and copy the new unique element into this position.
            nums[slow] = nums[fast]
        }
    }

    // The length of the unique elements is the slow index + 1
    return slow + 1
}

func main() {
    nums := []int{0, 0, 1, 1, 1, 2, 2, 3, 3, 4}
    k := removeDuplicates(nums)
    
    fmt.Printf("Number of unique elements: %d\n", k)
    fmt.Printf("Modified array (first %d elements): %v\n", k, nums[:k])
}

```

## Performance

* **Time Complexity:** $`O(n)`$
* We only iterate through the array once using the `fast` pointer. $n$ is the total number of elements in the array.


* **Space Complexity:** $`O(1)`$
* The algorithm operates strictly in-place. We only use two integer variables (`slow` and `fast`) to track indices, requiring zero extra memory scaling with the input size.
