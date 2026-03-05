# LeetCode 42: Trapping Rain Water

This is a classic coding problem. It looks intimidating at first, but the core logic breaks down beautifully once you see the pattern.

Here is a clear breakdown of the problem, the core logic, and the optimal solution using Golang.

## The Core Concept

To solve this, we have to look at each individual position in the elevation map. The amount of water that can be trapped above any specific bar `i` depends on two things:

1. The **tallest bar to its left** (`leftMax`).
2. The **tallest bar to its right** (`rightMax`).

The water trapped directly above bar `i` will always be determined by the shorter of those two highest bars, minus the height of the current bar itself.

* **Formula:** `min(leftMax, rightMax) - height[i]`

If the current bar is taller than or equal to the water level, it traps `0` water.

## The Optimal Solution: Two Pointers

While you can pre-calculate the `leftMax` and `rightMax` for every position and store them in arrays, that takes extra memory ($`O(n)`$ space).

The best way to solve this is using the **Two Pointers technique**. We place one pointer at the beginning of the array and one at the end. We move them inward, always moving the pointer that has the smaller maximum height.

Why does this work? Because if `leftMax` is smaller than `rightMax`, we don't need to know exactly how tall the right side gets. The smaller `leftMax` will strictly determine the water level for the left side.

### Golang Implementation

Here is the $`O(n)`$ time and $`O(1)`$ space complexity solution in Go:

```go
package main

import "fmt"

func trap(height []int) int {
    // Initialize two pointers
    left := 0
    right := len(height) - 1

    // Keep track of the maximum heights seen so far from both sides
    leftMax := height[left]
    rightMax := height[right]
    
    waterTrapped := 0

    // Loop until the two pointers meet
    for left < right {
        // We always process the side with the smaller max height
        if leftMax < rightMax {
            left++ // Move left pointer inward
            
            // If the new bar is taller, update leftMax
            if height[left] > leftMax {
                leftMax = height[left]
            } else {
                // Otherwise, it can trap water
                waterTrapped += leftMax - height[left]
            }
        } else {
            right-- // Move right pointer inward
            
            // If the new bar is taller, update rightMax
            if height[right] > rightMax {
                rightMax = height[right]
            } else {
                // Otherwise, it can trap water
                waterTrapped += rightMax - height[right]
            }
        }
    }

    return waterTrapped
}

func main() {
    elevationMap := []int{0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1}
    fmt.Println("Total water trapped:", trap(elevationMap)) // Output: 6
}

```

### Why this approach is great:

* **Time Complexity:** $`O(n)`$ because we only loop through the array once. Both pointers meet in the middle.
* **Space Complexity:** $`O(1)`$ because we only use a few integer variables (`left`, `right`, `leftMax`, `rightMax`, `waterTrapped`), regardless of how large the input array is.
