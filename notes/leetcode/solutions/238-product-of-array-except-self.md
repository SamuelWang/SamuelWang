# LeetCode 238: Product of Array Except Self

One of the classic challenges in algorithmic interviews is LeetCode 238, "Product of Array Except Self." It tests a developer's ability to manipulate arrays efficiently while strictly adhering to time and space complexity constraints.

This article breaks down the problem, explores the limitations of a naive approach, and demonstrates an optimized $`O(n)`$ solution using Go.

## The Problem Statement

Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

**Example:**

* **Input:** `[1, 2, 3, 4]`
* **Output:** `[24, 12, 8, 6]`

### Critical Constraints

To solve this correctly, the solution must meet three specific requirements:

1. **Time Complexity:** $`O(n)`$. You must traverse the array linearly.
2. **No Division:** You cannot calculate the total product of the array and simply divide by `nums[i]`. This constraint forces you to compute the product constructively and handles the edge case where the array contains zeros (which would cause a divide-by-zero error).
3. **Space Complexity:** $`O(1)`$. The solution must not use extra space proportional to the input size. (Note: The output array does not count as extra space for the purpose of space complexity analysis).

## The Architectural Approach: Prefix and Suffix Products

Since division is forbidden, the product for any given index `i` can be viewed as the combination of two parts:

1. The product of all numbers to the **left** of `i` (Prefix).
2. The product of all numbers to the **right** of `i` (Suffix).

Mathematically, this looks like:

$$Answer[i] = (\text{Product Left of } i) \times (\text{Product Right of } i)$$

### The Space Optimization Strategy

A naive approach might use two separate arrays, `L` and `R`, to store the prefix and suffix products respectively. While this satisfies the time constraint, it requires $`O(n)`$ extra space.

To achieve $`O(1)`$ space, we utilize the **output array** to hold intermediate results:

1. **First Pass (Left-to-Right):** Compute the prefix products and store them directly in the `answer` array.
2. **Second Pass (Right-to-Left):** Maintain a running variable for the suffix product. Multiply this running product with the value already stored in the `answer` array.

## Go Implementation

The following Go code implements this two-pass logic. It is idiomatic, clean, and avoids unnecessary memory allocation.

```go
package main

import "fmt"

// ProductExceptSelf calculates the product of all elements except the one at the current index.
// Time Complexity: O(n)
// Space Complexity: O(1) (excluding the output array)
func ProductExceptSelf(nums []int) []int {
    n := len(nums)
    
    // Guard clause for edge cases, though problem constraints usually state n >= 2
    if n == 0 {
        return []int{}
    }

    // Step 1: Initialize the answer array.
    // This array will hold the "Left Products" (Prefix) after the first pass.
    answer := make([]int, n)

    // Step 2: Calculate Left Products (Prefix)
    // answer[i] should contain the product of nums[0]...nums[i-1]
    answer[0] = 1 // No elements to the left of index 0, so identity is 1
    for i := 1; i < n; i++ {
        answer[i] = nums[i-1] * answer[i-1]
    }

    // Step 3: Calculate Right Products (Suffix) and finalize
    // Instead of allocating a new array, use a scalar variable 'rightProduct'
    // to track the running product from the right side.
    rightProduct := 1
    for i := n - 1; i >= 0; i-- {
        // Multiply the existing Left Product (stored in answer[i]) by the current Right Product
        answer[i] = answer[i] * rightProduct
        
        // Update the Right Product for the next iteration (moving leftwards)
        rightProduct *= nums[i]
    }

    return answer
}

func main() {
    input := []int{1, 2, 3, 4}
    result := ProductExceptSelf(input)
    fmt.Printf("Input: %v\nOutput: %v\n", input, result) 
}

```

## Step-by-Step Execution Trace

Let's trace the execution using the input `[1, 2, 3, 4]`.

### Phase 1: Left Products (Prefix)

The goal is for `answer[i]` to equal the product of everything to the left of `i`.

* Initialize `answer[0] = 1`.
* `i=1`: `nums[0]` is 1. `answer[1]` becomes `1 * 1 = 1`.
* `i=2`: `nums[1]` is 2. `answer[2]` becomes `1 * 2 = 2`.
* `i=3`: `nums[2]` is 3. `answer[3]` becomes `2 * 3 = 6`.

**State of array after Phase 1:** `[1, 1, 2, 6]`

### Phase 2: Right Products (Suffix) & Aggregation

We iterate backward. We track a running variable `R`, initialized to 1.

* **Index 3:**
* Current `answer[3]` (Prefix) is 6.
* Result: `6 * R(1) = 6`.
* Update `R`: `R * nums[3]` -> `1 * 4 = 4`.


* **Index 2:**
* Current `answer[2]` (Prefix) is 2.
* Result: `2 * R(4) = 8`.
* Update `R`: `R * nums[2]` -> `4 * 3 = 12`.


* **Index 1:**
* Current `answer[1]` (Prefix) is 1.
* Result: `1 * R(12) = 12`.
* Update `R`: `R * nums[1]` -> `12 * 2 = 24`.


* **Index 0:**
* Current `answer[0]` (Prefix) is 1.
* Result: `1 * R(24) = 24`.
* Update `R`: `R * nums[0]` -> `24 * 1 = 24`.



**Final Output:** `[24, 12, 8, 6]`

## Complexity Analysis

* **Time Complexity:** $`O(n)`$. The algorithm involves exactly two passes over the array (one forward, one backward). Since both operations are linear, the total time complexity is linear.
* **Space Complexity:** $`O(1)`$. We use one integer slice for the output (which is allowed) and a single integer variable (`rightProduct`) for the calculation. No additional data structures are allocated.

This approach ensures the solution is robust, efficient, and handles edge cases like zeros without the risks associated with division.
