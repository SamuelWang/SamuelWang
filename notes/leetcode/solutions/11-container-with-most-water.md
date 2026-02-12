# LeetCode 11: Container With Most Water

The "Container With Most Water" problem is a classic algorithmic challenge often encountered in technical interviews. It tests your ability to optimize a solution from a brute-force $`O(n^2)`$ approach to a linear $`O(n)`$ solution using the **Two Pointer** technique.

## 1. Problem Description

You are given an integer array `height` of length $`n`$. These integers represent the height of vertical lines drawn at specific indices.

* The endpoints of the $`i^{th}`$ line are $`(i, 0)`$ and $`(i, height[i])`$.
* You must find two lines that, together with the x-axis, form a container that holds the most water.
* The volume of water is defined as the **Area** of the rectangle formed by the two lines.

**The Formula:**
The area is determined by the distance between the lines (width) multiplied by the height of the *shorter* line (water spills over the taller one).

$$Area = \min(height[left], height[right]) \times (right - left)$$

## 2. The Strategy: Two Pointers

A brute-force approach would check every single pair of lines, resulting in $`O(n^2)`$ time complexity. This is inefficient for large datasets.

The optimal strategy uses **Two Pointers** to solve this in a single pass:

1. **Maximize Width:** Start with one pointer at the very beginning (`left`) and one at the very end (`right`). This guarantees the maximum possible width.
2. **Greedy Iteration:** At every step, calculate the area. Then, make a decision to move one pointer:
* The area is limited by the **shorter** line.
* If you move the taller line, the width decreases, and the height can only stay the same or decrease. You cannot possibly find a larger area this way.
* Therefore, you must **move the pointer of the shorter line inward**, hoping to find a taller line that compensates for the loss in width.



## 3. Step-by-Step Example

Let's trace the algorithm with the following input:
**Input:** `[1, 8, 6, 2, 5, 4, 8, 3, 7]`

We start with `left` at index 0 and `right` at index 8.

| Step | Left Index (Val) | Right Index (Val) | Width  | Height  | Current Area | Max Area | Decision |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **1** | **0** (1) | **8** (7) | 8 | 1 | **8** | 8 | 1 < 7, Move **Left** |
| **2** | **1** (8) | **8** (7) | 7 | 7 | **49** | 49 | 8 > 7, Move **Right** |
| **3** | **1** (8) | **7** (3) | 6 | 3 | **18** | 49 | 8 > 3, Move **Right** |
| **4** | **1** (8) | **6** (8) | 5 | 8 | **40** | 49 | 8 == 8, Move **Right** |
| **5** | **1** (8) | **5** (4) | 4 | 4 | **16** | 49 | 8 > 4, Move **Right** |
| **6** | **1** (8) | **4** (5) | 3 | 5 | **15** | 49 | 8 > 5, Move **Right** |
| **7** | **1** (8) | **3** (2) | 2 | 2 | **4** | 49 | 8 > 2, Move **Right** |
| **8** | **1** (8) | **2** (6) | 1 | 6 | **6** | 49 | Loop Ends |

**Result:** The maximum area found is **49** (from Step 2).

## 4. Implementation in Golang

Here is the clean, idiomatic Go solution implementing the logic above.

```go
func maxArea(height []int) int {
    // Initialize pointers at both ends
    left := 0
    right := len(height) - 1
    
    maxWater := 0

    for left < right {
        // Calculate the width between lines
        width := right - left
        
        // Determine the height of the container (limited by the shorter line)
        // and move the pointer of the shorter line inward.
        h := 0
        if height[left] < height[right] {
            h = height[left]
            left++ 
        } else {
            h = height[right]
            right--
        }

        // Calculate current area
        currentArea := width * h

        // Update maxWater if currentArea is larger
        if currentArea > maxWater {
            maxWater = currentArea
        }
    }

    return maxWater
}

```

## 5. Complexity Analysis

* **Time Complexity:** $`O(n)`$.
We traverse the array exactly once. The `left` and `right` pointers move towards each other and meet in the middle, processing each element a single time.
* **Space Complexity:** $`O(1)`$.
We only use a fixed number of variables (`left`, `right`, `maxWater`, `h`, `width`) to store the state. No extra data structures are allocated.
