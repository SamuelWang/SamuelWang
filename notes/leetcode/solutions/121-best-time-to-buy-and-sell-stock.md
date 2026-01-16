# LeetCode 121: Best Time to Buy and Sell Stock

## 1. Problem Analysis

**The Goal:**
You are given an array `prices` where `prices[i]` is the given stock price on the $`i^{th}`$ day. You want to maximize your profit by choosing a single day to buy one stock and choosing a **different day in the future** to sell that stock. If you cannot achieve any profit, return 0.

**Key Constraints:**

* You must buy before you sell (index of buy < index of sell).
* We need to find the maximum difference between two numbers in the array such that the larger number appears after the smaller number.

## 2. Architectural Approach

### The Naive Approach (Brute Force)

A junior developer might initially solve this by comparing every pair of days.

* **Logic:** For each day, look at every subsequent day and calculate the profit.
* **Drawback:** This results in a Time Complexity of $`O(n^2)`$. If you have 10,000 days of data, that is $`100,000,000`$ operations. In a high-throughput system, this is a bottleneck we must avoid.

### The Optimized Approach (One Pass)

To solve this efficiently, we only need to iterate through the array once. We maintain two variables as we traverse:

1. **`minPrice`**: The lowest price we have seen *so far*.
2. **`maxProfit`**: The maximum profit we can calculate at the current step (Current Price - `minPrice`).

**The Logic:**

* Initialize `minPrice` to a very high number (or the first element).
* Initialize `maxProfit` to 0.
* Walk through the array:
* If the current price is **lower** than `minPrice`, we have found a better buying opportunity. Update `minPrice`.
* Else if the profit calculated (`price - minPrice`) is **higher** than `maxProfit`, we have found a better selling opportunity. Update `maxProfit`.

This reduces Time Complexity to $`O(n)`$, which is linear and highly scalable.

## 3. Idiomatic Go Implementation

In Go, we value clarity and simplicity. While we could use `math.Max` or `math.Min`, those functions operate on `float64`, requiring type conversion which adds unnecessary overhead. It is more performant and idiomatic in Go to use simple `if` statements for integer comparisons.

```go
package main

import (
	"fmt"
	"math"
)

// maxProfit calculates the maximum profit from a single buy and sell transaction.
func maxProfit(prices []int) int {
	// 1. Handle edge cases: If there are fewer than 2 data points,
	// a transaction (buy AND sell) is impossible.
	if len(prices) < 2 {
		return 0
	}

	// 2. Initialize variables
	// We set minPrice to the maximum possible integer value initially,
	// or simply the first element of the array.
	minPrice := math.MaxInt64 
	maxProfit := 0

	// 3. Iterate through the prices once (O(n))
	for _, currentPrice := range prices {
		// Update minimum price if we find a cheaper buying day
		if currentPrice < minPrice {
			minPrice = currentPrice
		} else {
			// Calculate potential profit if we sold today
			currentProfit := currentPrice - minPrice
			
			// Update maxProfit if selling today beats our previous best
			if currentProfit > maxProfit {
				maxProfit = currentProfit
			}
		}
	}

	return maxProfit
}

func main() {
	prices := []int{7, 1, 5, 3, 6, 4}
	fmt.Printf("Max Profit: %d\n", maxProfit(prices)) // Output: 5 (Buy at 1, Sell at 6)
}

```

## 4. Code Review & Performance Analysis

### Time Complexity: $`O(n)`$

We iterate through the `prices` slice exactly once. Whether the input is 100 days or 1,000,000 days, the execution time grows linearly.

### Space Complexity: $`O(1)`$

We are not creating auxiliary data structures (like maps or extra arrays). We only use two variables (`minPrice` and `maxProfit`) regardless of the input size. This is memory efficient.

### Go Best Practices Used:

* **Range Loop:** We use `for _, currentPrice := range prices` which is the standard way to iterate over slices in Go.
* **Zero Values:** We initialize `maxProfit` to 0, which also handles the case where prices strictly decrease (e.g., `[7, 6, 4, 3, 1]`), correctly returning 0.
* **Edge Case Handling:** The check `len(prices) < 2` prevents index out-of-range errors or illogical operations on empty/single-item arrays.

## 5. Principal Engineer's Note

In a real-world trading system, this algorithm is the foundation for more complex strategies. However, be aware of **concurrency**. If this `prices` stream was live-updating, you would need to implement a thread-safe mechanism (like a `mutex` or `atomic` values) or use Go channels to process the stream without race conditions.
