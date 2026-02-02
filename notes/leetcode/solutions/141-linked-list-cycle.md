# LeetCode 141: Linked List Cycle

In technical interviews, Linked List problems are a staple. One of the most classic questions is **LeetCode 141: Linked List Cycle**.

While there are multiple ways to solve this (such as using a Hash Map), the most efficient solution uses a "Two Pointer" technique known as **Floyd’s Cycle-Finding Algorithm**.

## 1. The Problem

You are given the `head` of a singly linked list. You need to return `true` if there is a cycle (a loop) in the list, and `false` if the list ends normally.

* **Normal List:** The last node points to `nil`.
* **Cyclic List:** The last node points back to a previous node, creating an infinite loop.

## 2. The Solution: The "Tortoise and Hare"

The most memory-efficient way to solve this is using two pointers moving at different speeds.

1. **The Tortoise (`slow`):** Moves **1 step** at a time.
2. **The Hare (`fast`):** Moves **2 steps** at a time.

### The Logic

If there is no loop, the `fast` runner will simply reach the end (`nil`) first. However, if there is a loop, the `fast` runner is trapped inside the track. Eventually, it will lap the `slow` runner. When they land on the same node, we know a cycle exists.

## 3. Addressing the Common Doubt: "Will they miss each other?"

A common concern is: *In a large loop, won't the Fast pointer jump over the Slow pointer and miss the collision?*

**The Answer:** No. It is mathematically impossible for them to miss each other.

Think of it in terms of **Relative Speed**.

* `fast` moves 2 steps.
* `slow` moves 1 step.
* Therefore, `fast` gets closer to `slow` by exactly **1 step** every turn.

Because the distance between them decreases by exactly 1 node at a time, `fast` cannot "jump over" `slow`. It will gradually close the gap until the distance is zero.

### Step-by-Step Trace (Large List)

Let's trace this on a 6-node loop: `1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 1`.

* `slow` starts at Node 1.
* `fast` starts at Node 2 (one step ahead).

| Iteration | Slow Position | Fast Position | The Logic |
| --- | --- | --- | --- |
| **Start** | **Node 1** | **Node 2** | Gap is small. |
| **1** | **Node 2** | **Node 4** | `slow` moves 1, `fast` moves 2. |
| **2** | **Node 3** | **Node 6** | `slow` moves 1, `fast` moves 2. |
| **3** | **Node 4** | **Node 2** | `fast` hits the end (6), loops back to 1, then 2. |
| **4** | **Node 5** | **Node 4** | `fast` is now right behind `slow`. |
| **5** | **Node 6** | **Node 6** | **Collision!** The pointers meet. Return `true`. |

## 4. The Golang Implementation

Here is the clean, production-ready Go code.

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 * Val int
 * Next *ListNode
 * }
 */

func hasCycle(head *ListNode) bool {
    // 1. Edge Case: Empty list or single node cannot have a cycle
    // (unless it points to itself, which the loop handles, but this is a safety check)
    if head == nil || head.Next == nil {
        return false
    }

    // 2. Initialize Pointers
    // You can start both at head, or split them immediately.
    // Here, we split them to avoid a "do-while" logic.
    slow := head
    fast := head.Next

    // 3. The Race
    for slow != fast {
        // If fast hits the end, there is no cycle.
        if fast == nil || fast.Next == nil {
            return false
        }

        // Move pointers
        slow = slow.Next      // 1 step
        fast = fast.Next.Next // 2 steps
    }

    // 4. If the loop exits, slow == fast, so a cycle exists.
    return true
}

```

## 5. Complexity Analysis

Why is this better than using a `map[ *ListNode ]bool` to track visited nodes?

### Time Complexity: $`O(N)`$

* **No Cycle:** The `fast` pointer reaches the end in $`N/2`$ steps.
* **Cycle:** The `fast` pointer catches the `slow` pointer roughly within one traversal of the loop. It is linear time.

### Space Complexity: $`O(1)`$

This is the key advantage.

* **Hash Map Approach:** Would require $`O(N)`$ memory to store every node we visit.
* **Two Pointer Approach:** Only requires storage for two variables (`slow` and `fast`), regardless of whether the list has 10 nodes or 10 million nodes.
