# LeetCode Queue Problems

The Queue is a linear data structure that operates on the First-In-First-Out (FIFO) principle. While simple in concept—elements are added to the back (enqueued) and removed from the front (dequeued)—the queue serves as a critical building block in computer science.

In system design, queues are essential for decoupling architecture (such as message brokers like Kafka or RabbitMQ) and managing asynchronous tasks. In algorithmic problem solving, queues are the primary mechanism driving Breadth-First Search (BFS) and are used in advanced sliding window optimizations via the Monotonic Queue pattern.

The following is a curated list of LeetCode problems designed to build proficiency with Queues, ranging from basic mechanics to complex algorithmic optimizations.

## 1. Foundational Mechanics

This category focuses on understanding the raw operations of FIFO structures and Ring Buffers. Mastering these problems ensures a solid grasp of how queues manage memory and data flow.

* **[232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)** (Easy)
  * **Concept:** This problem requires inverting data flows using two stacks to simulate a queue. It is a common exercise to understand the cost of operations (amortized $`O(1)`$).

* **[622. Design Circular Queue](https://leetcode.com/problems/design-circular-queue/)** (Medium)
  * **Concept:** This implementation acts as a "Ring Buffer." Circular queues are vital in low-latency systems and network drivers because they utilize fixed-size arrays, avoiding the overhead of dynamic memory allocation during repeated enqueue/dequeue operations.

* **[933. Number of Recent Calls](https://leetcode.com/problems/number-of-recent-calls/)** (Easy)
  * **Concept:** A practical application of a queue to maintain a rolling time window, discarding outdated timestamps from the front while adding new ones to the back.



## 2. Breadth-First Search (BFS)

In graph and tree traversal algorithms, the Queue is the standard tool for state management. It allows for processing nodes layer-by-layer, making it the algorithm of choice for finding the shortest path in unweighted graphs.

* **[102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)** (Medium)
  * **Concept:** The standard approach to traversing a tree level by level.

* **[994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)** (Medium)
  * **Concept:** **Multi-source BFS.** Instead of starting from a single node, the queue is initialized with multiple sources simultaneously. This simulates parallel processing (e.g., spreading infection) and is a frequent interview pattern.

* **[1926. Nearest Exit from Entrance in Maze](https://leetcode.com/problems/nearest-exit-from-entrance-in-maze/)** (Medium)
  * **Concept:** Represents a standard shortest path search in a grid environment using BFS.

* **[127. Word Ladder](https://leetcode.com/problems/word-ladder/)** (Hard)
  * **Concept:** Requires implicit graph construction combined with BFS. The challenge lies in transforming words by changing one letter at a time to reach a target word in the fewest steps.



## 3. Monotonic Queues (Sliding Window Optimization)

The Monotonic Queue is an optimization technique used to find the maximum or minimum value within a sliding window in $`O(1)`$ amortized time. By maintaining a deque (double-ended queue) where elements are strictly increasing or decreasing, an $`O(N \cdot K)`$ naive solution can be optimized to $`O(N)`$.

* **[239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)** (Hard)
  * **Concept:** The definitive Monotonic Queue problem. It requires processing a sliding window across an array and returning the maximum value for each window position efficiently.

* **[1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/)** (Medium)
  * **Concept:** This problem often requires maintaining two deques simultaneously (one increasing, one decreasing) to track the min and max within the current window to satisfy the condition.

* **[862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/)** (Hard)
  * **Concept:** A complex problem that combines Prefix Sums with a Monotonic Deque. It is widely cited in competitive programming as a test of deeper data structure understanding.



## 4. System Design & Concurrency

These problems move beyond pure algorithms to simulate real-world backend engineering scenarios, particularly focusing on streams and threading.

* **[346. Moving Average from Data Stream](https://leetcode.com/problems/moving-average-from-data-stream/)** (Easy - Premium)
  * **Concept:** Simulates processing a live data feed (stream processing) where the total dataset size is unknown or infinite.

* **[1188. Design Bounded Blocking Queue](https://leetcode.com/problems/design-bounded-blocking-queue/)** (Medium - Premium)
  * **Concept:** **Concurrency.** This requires implementing a thread-safe queue that blocks the producer when full and the consumer when empty. This is the implementation of the Producer-Consumer design pattern.

## Suggested Learning Path

Phase 1 — Foundations

- [933. Number of Recent Calls](https://leetcode.com/problems/number-of-recent-calls/) — rolling-window maintenance; basic enqueue/dequeue.
- [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/) — simulate queue semantics with two stacks.
- [622. Design Circular Queue](https://leetcode.com/problems/design-circular-queue/) — ring buffer, fixed-size array trade-offs.

Phase 2 — BFS Basics

- [102. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/) — layer-by-layer traversal using a queue.
- [994. Rotting Oranges](https://leetcode.com/problems/rotting-oranges/) — multi-source BFS and time-step propagation on grids.
- [1926. Nearest Exit from Entrance in Maze](https://leetcode.com/problems/nearest-exit-from-entrance-in-maze/) — shortest path in a grid.

Phase 3 — Sliding Window & Monotonic Queues

- [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) — monotonic deque for O(N) sliding-window maxima.
- [1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/) — track min/max with two deques.
- [862. Shortest Subarray with Sum at Least K](https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/) — prefix sums + monotonic deque.

Phase 4 — Advanced Patterns & Systems

- [127. Word Ladder](https://leetcode.com/problems/word-ladder/) — implicit graph + BFS for shortest transformation.
- [346. Moving Average from Data Stream](https://leetcode.com/problems/moving-average-from-data-stream/) — streaming/windowed aggregates.
- [1188. Design Bounded Blocking Queue](https://leetcode.com/problems/design-bounded-blocking-queue/) — concurrency: producer/consumer and blocking semantics.
