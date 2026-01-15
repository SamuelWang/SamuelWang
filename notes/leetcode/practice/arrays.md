# LeetCode Arrays - Patterns and Scalability

Arrays are the bedrock of system design—they are all about **contiguous memory allocation**, which translates to CPU cache friendliness and predictable access times. When we optimize array operations, we are often optimizing for data locality, a critical concept in high-performance computing and low-latency systems.

Here is a breakdown of array-related problems by architectural pattern, ranging from "must-know" basics to complex optimization techniques.

## 1. The Fundamentals: Memory & Iteration

*Why it matters:* Before tackling complex algorithms, you must understand how to manipulate indices and handle edge cases (empty arrays, boundary checks) without off-by-one errors. This is the equivalent of writing clean, bug-free utility functions.

* **Easy:** [Two Sum](https://leetcode.com/problems/two-sum/) (The classic hash map vs. brute force trade-off)
* **Easy:** [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
* **Easy:** [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)
* **Medium:** [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/) (Teaches pre-computation and space optimization)

## 2. The "Two Pointers" Technique

*Why it matters:* In distributed systems or stream processing, we often process data from multiple sources or ends. Two pointers allow you to process a linear data structure in $`O(N)`$ time with $`O(1)`$ space, avoiding the memory overhead of creating new data structures.

* **Easy:** [Move Zeroes](https://leetcode.com/problems/move-zeroes/)
* **Medium:** [Container With Most Water](https://leetcode.com/problems/container-with-most-water/) (Greedy optimization)
* **Medium:** [3Sum](https://leetcode.com/problems/3sum/) (Handling duplicates and multiple pointers)
* **Hard:** [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/) (A classic interview favorite involving pre-computation or two pointers)

## 3. Sliding Window

*Why it matters:* This is essentially **flow control** and **rate limiting**. In networking (TCP windows) or analytics (rolling averages), we constantly look at a subset of data moving through a stream.

* **Medium:** [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/) (Kadane's Algorithm - dynamic programming optimized to $`O(1)`$ space)
* **Medium:** [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
* **Medium:** [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
* **Hard:** [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) (Requires a Monotonic Queue—highly relevant for heavy-hitter algorithms)

## 4. Matrix (2D Arrays)

*Why it matters:* Matrices map directly to **image processing**, **grids in geospatial services**, and **graph adjacency matrices**. Handling 2D arrays requires careful index management to avoid cache misses (row-major vs. column-major traversal).

* **Medium:** [Rotate Image](https://leetcode.com/problems/rotate-image/) (In-place memory manipulation)
* **Medium:** [Set Matrix Zeroes](https://leetcode.com/problems/set-matrix-zeroes/)
* **Medium:** [Spiral Matrix](https://leetcode.com/problems/spiral-matrix/)
* **Medium:** [Word Search](https://leetcode.com/problems/word-search/) (Introduction to Backtracking/DFS on grids)

## 5. Prefix Sum & Hashing

*Why it matters:* Prefix sums are used heavily in **immutable data structures** and **range query optimizations** (like in SQL databases or time-series DBs). They allow $`O(1)`$ retrieval of range aggregates after $`O(N)`$ preprocessing.

* **Easy:** [Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/)
* **Medium:** [Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/) (Combines prefix sum with hashing)
* **Medium:** [Contiguous Array](https://leetcode.com/problems/contiguous-array/)

## Note on Scalability

When reviewing array solutions, I look for **Space Complexity**.

* **Junior Dev:** Solves `Two Sum` by creating a new array for every iteration ($`O(N^2)`$ space/time).
* **Senior Dev:** Uses a Hash Map ($`O(N)`$ space/time).
* **Staff/Principal:** Asks if the input array is sorted. If so, they switch to Two Pointers to achieve $`O(N)`$ time with **$`O(1)`$ space**. In embedded systems or massive data pipelines, saving that memory is the difference between a system that crashes and one that scales.

