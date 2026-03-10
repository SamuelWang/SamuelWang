# LeetCode 27: Remove Element

Here is an explanation and a solution for the **LeetCode 27: Remove Element** problem using Golang.

## Understanding the Problem

You are given an array of integers `nums` and a specific integer `val`.
Your task is to remove all occurrences of `val` from the array **in-place** (without creating a new array) and return the new length of the array.

The elements that are not equal to `val` must be shifted to the front of the array. It does not matter what you leave beyond the new length.

## The Approach: Two Pointers

The best way to solve this is by using the **Two Pointers** technique. Since we need to modify the array in-place, we can use two markers (indices) to keep track of our position:

* **Reader Pointer (`i`):** This pointer iterates through every element in the array to check its value.
* **Writer Pointer (`k`):** This pointer keeps track of the position where the next valid element (an element not equal to `val`) should be placed.

**How it works step-by-step:**

1. Start both pointers at the beginning of the array (index 0).
2. Look at the number at the reader pointer.
3. If the number is **equal** to `val`, we just ignore it and move the reader forward.
4. If the number is **not equal** to `val`, we copy it to the spot marked by the writer pointer. Then, we move both the reader and the writer forward by one.
5. When the reader finishes scanning the array, the writer pointer `k` will hold the exact count of valid elements, which is also the new length of the array.

## 3. Golang Solution

Here is the clean and efficient implementation in Go:

```go
func removeElement(nums []int, val int) int {
    // 'k' is our writer pointer. It also acts as the counter for valid elements.
    k := 0 
    
    // 'i' is our reader pointer, iterating through the array.
    for i := 0; i < len(nums); i++ {
        // If the current element is NOT the value we want to remove
        if nums[i] != val {
            // Place it at the 'k' index
            nums[k] = nums[i]
            // Increment the writer pointer
            k++
        }
    }
    
    // Return the number of elements not equal to 'val'
    return k
}

```

## 4. Complexity Analysis

* **Time Complexity:** $`O(n)`$, where $n$ is the number of elements in `nums`. We only loop through the array exactly one time.
* **Space Complexity:** $`O(1)`$. We are only using a single extra variable (`k`), meaning we are modifying the array entirely in-place without using extra memory.
