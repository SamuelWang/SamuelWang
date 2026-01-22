# LeetCode Linked List Problems

Linked Lists remain a staple of technical interviews not because they are frequently implemented from scratch in modern application development, but because they serve as an excellent proxy for testing a developer's understanding of memory management and pointer manipulation.

Solving these problems requires a strong grasp of references, edge-case handling (such as null pointers), and algorithmic efficiency. Below is a curated list of common Linked List problems on LeetCode, categorized by the architectural patterns required to solve them.

## 1. The "Two Pointer" (Runner) Technique

This is the most prevalent pattern for Linked List problems. It typically involves using two pointers moving at different speeds (e.g., a "slow" pointer moving one step and a "fast" pointer moving two steps) or delaying one pointer to identify specific properties of the list structure.

**Key Concepts:** Cycle detection, finding midpoints, and maintaining fixed-distance gaps.

* **[Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)** (Easy)
* **[Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)** (Easy)
* **[Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)** (Medium)
* **[Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)** (Medium)
* **[Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)** (Easy)

## 2. Manipulation & Reversal

These problems test the ability to maintain state (`prev`, `curr`, `next`) while mutating connections. They are often sources of "off-by-one" errors and disconnected lists. Solutions frequently utilize a "Dummy Head" (sentinel node) to simplify edge cases where the head of the list might change.

**Key Concepts:** In-place reversal, sub-segment manipulation, and pointer hygiene.

* **[Delete Node in a Linked List](https://leetcode.com/problems/delete-node-in-a-linked-list/)** (Easy)
* **[Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)** (Easy)
* **[Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)** (Medium)
* **[Flatten a Multilevel Doubly Linked List](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/)** (Medium)
* **[Odd Even Linked List](https://leetcode.com/problems/odd-even-linked-list/)** (Medium)
* **[Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)** (Medium)
* **[Partition List](https://leetcode.com/problems/partition-list/)** (Medium)
* **[Reorder List](https://leetcode.com/problems/reorder-list/)** (Medium)
* **[Remove Duplicates from Sorted List II](httpshttps://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)** (Medium)
* **[Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)** (Medium)
* **[Rotate List](https://leetcode.com/problems/rotate-list/)** (Medium)
* **[Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)** (Hard)

## 3. Merging & Sorting

These problems typically apply Divide and Conquer strategies or Heap data structures to Linked Lists. Unlike arrays, Merge Sort is often preferred for Linked Lists because it does not require random access.

**Key Concepts:** Recursion, Min-Heaps (Priority Queues), and Merge Sort.

* **[Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)** (Easy)
* **[Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)** (Hard)
* **[Sort List](https://leetcode.com/problems/sort-list/)** (Medium)

## 4. System Design Primitives

These problems move beyond algorithmic puzzles and require implementing actual data structures used in production systems (such as caching mechanisms). They often involve combining a Hash Map (for fast lookup) with a Doubly Linked List (for fast insertion/deletion ordering).

**Key Concepts:** LRU/LFU eviction policies and state management.

* **[LRU Cache](https://leetcode.com/problems/lru-cache/)** (Medium)
* **[LFU Cache](https://leetcode.com/problems/lfu-cache/)** (Hard)
* **[Design Browser History](https://leetcode.com/problems/design-browser-history/)** (Medium)

## 5. Deep Copying

This category focuses on cloning data structures that have complex internal references. The challenge lies in creating a deep copy where the new nodes point to other new nodes, rather than referencing the original list's memory.

**Key Concepts:** Hash Map mapping and node interleaving.

* **[Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)** (Medium)

## Suggested Learning Path

- **Begin with fundamentals:** review node structure, traversal, and pointer manipulation.
- **Phase 1 — Two-pointer problems:** solve *[Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)*, *[Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)*, *[Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)*.
 - **Phase 2 — Reversal & manipulation:** practice *[Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)*, *[Delete Node in a Linked List](https://leetcode.com/problems/delete-node-in-a-linked-list/)*, *[Add Two Numbers](https://leetcode.com/problems/add-two-numbers/)*, *[Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/)*, *[Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)*.
 - **Phase 3 — Merging & sorting:** tackle *[Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)*, *[Sort List](https://leetcode.com/problems/sort-list/)*, then *[Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)*.
 - **Phase 4 — System-design primitives:** implement *[LRU Cache](https://leetcode.com/problems/lru-cache/)*, *[LFU Cache](https://leetcode.com/problems/lfu-cache/)*, *[Design Browser History](https://leetcode.com/problems/design-browser-history/)* to combine maps and doubly-linked lists.
 - **Phase 5 — Advanced & deep-copy:** work on *[Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)*, *[Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)*, and other hard problems.
- **Practice checklist:** implement each solution, add tests, write time/space complexity, and explain edge cases.

Happy practicing — repeat cycles until you're comfortable with all categories.
