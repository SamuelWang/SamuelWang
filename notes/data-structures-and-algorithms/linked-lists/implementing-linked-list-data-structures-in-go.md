# Implementing Linked List Data Structures in Go

In the Go programming language, the slice is the primary linear data structure used for day-to-day development due to its efficiency and ease of use. However, understanding and implementing Linked Lists remains a fundamental skill for solving specific architectural problems, such as building caches, queues, or ring buffers.

With the introduction of Generics in Go 1.18, developers can now create type-safe, reusable data structures without relying on `interface{}` and runtime reflection. This article explores the implementation of Singly, Doubly, and Circular Linked Lists using modern Go syntax.

## 1. The Singly Linked List (SLL)

A Singly Linked List consists of nodes where each node contains data and a pointer to the next node in the sequence. It allows for efficient insertion and deletion at the beginning of the list but requires sequential traversal to access elements.

### Implementation

The core components are the `Node` struct (holding the value and the pointer) and the `LinkedList` struct (holding references to the head and tail).

```go
package main

import "fmt"

// Node represents a single element in the list.
type Node[T any] struct {
    Value T
    Next  *Node[T]
}

// LinkedList controls the structure.
// Maintaining a Tail pointer allows for O(1) appends.
type LinkedList[T any] struct {
    Head *Node[T]
    Tail *Node[T]
    Size int
}

// Append adds a new node to the end of the list.
// Complexity: O(1)
func (list *LinkedList[T]) Append(value T) {
    newNode := &Node[T]{Value: value}

    if list.Head == nil {
        list.Head = newNode
        list.Tail = newNode
    } else {
        list.Tail.Next = newNode
        list.Tail = newNode
    }
    list.Size++
}

// Prepend adds a new node to the start of the list.
// Complexity: O(1)
func (list *LinkedList[T]) Prepend(value T) {
    newNode := &Node[T]{Value: value}
    
    if list.Head == nil {
        list.Head = newNode
        list.Tail = newNode
    } else {
        newNode.Next = list.Head
        list.Head = newNode
    }
    list.Size++
}

// Traverse prints the list values.
func (list *LinkedList[T]) Traverse() {
    current := list.Head
    for current != nil {
        fmt.Printf("%v -> ", current.Value)
        current = current.Next
    }
    fmt.Println("nil")
}

```

## 2. The Doubly Linked List (DLL)

While Singly Linked Lists are memory efficient, they only allow traversal in one direction. Deleting a specific node (without knowing its predecessor) is an $`O(N)`$ operation.

A Doubly Linked List solves this by adding a `Prev` pointer to every node. This enables bidirectional traversal and allows for $`O(1)`$ node deletion if a reference to the node is already held. This structure is commonly used in Least Recently Used (LRU) caches.

### Implementation

Note the addition of the `Prev` field in the `DNode` struct and the logic to update it during insertion and deletion.

```go
type DNode[T any] struct {
    Value T
    Next  *DNode[T]
    Prev  *DNode[T]
}

type DoublyLinkedList[T any] struct {
    Head *DNode[T]
    Tail *DNode[T]
    Size int
}

// Append adds to the end.
func (list *DoublyLinkedList[T]) Append(value T) {
    newNode := &DNode[T]{Value: value}
    
    if list.Head == nil {
        list.Head = newNode
        list.Tail = newNode
    } else {
        newNode.Prev = list.Tail
        list.Tail.Next = newNode
        list.Tail = newNode
    }
    list.Size++
}

// DeleteNode removes a specific node from the list.
// Complexity: O(1)
func (list *DoublyLinkedList[T]) DeleteNode(node *DNode[T]) {
    if node == nil {
        return
    }

    // Handle Head case
    if node == list.Head {
        list.Head = node.Next
        if list.Head != nil {
            list.Head.Prev = nil
        }
    }

    // Handle Tail case
    if node == list.Tail {
        list.Tail = node.Prev
        if list.Tail != nil {
            list.Tail.Next = nil
        }
    }

    // Handle Middle case
    if node.Prev != nil && node.Next != nil {
        node.Prev.Next = node.Next
        node.Next.Prev = node.Prev
    }
    
    // Cleanup for Garbage Collector
    node.Next = nil
    node.Prev = nil
    list.Size--
}

```

## 3. The Circular Linked List (CLL)

A Circular Linked List connects the `Tail` node back to the `Head` rather than pointing to `nil`. This structure is useful for applications requiring continuous cycling through data, such as round-robin scheduling algorithms or ring buffers in streaming applications.

### Implementation

The traversal logic differs here; a standard loop checking for `nil` results in an infinite loop. Instead, the traversal checks if the pointer has returned to the `Head`.

```go
type CNode[T any] struct {
    Value T
    Next  *CNode[T]
}

type CircularLinkedList[T any] struct {
    Head *CNode[T]
    Tail *CNode[T]
    Size int
}

// Append adds to the list and maintains the loop.
func (list *CircularLinkedList[T]) Append(value T) {
    newNode := &CNode[T]{Value: value}

    if list.Head == nil {
        list.Head = newNode
        list.Tail = newNode
        newNode.Next = newNode // Points to itself
    } else {
        newNode.Next = list.Head
        list.Tail.Next = newNode
        list.Tail = newNode
    }
    list.Size++
}

// Traverse prints the list exactly once.
func (list *CircularLinkedList[T]) Traverse() {
    if list.Head == nil {
        return
    }

    current := list.Head
    for {
        fmt.Printf("%v -> ", current.Value)
        current = current.Next
        
        // Break if we circle back to start
        if current == list.Head {
            break
        }
    }
    fmt.Println("(Back to Head)")
}

```

## 4. Performance Comparison and Trade-offs

When choosing between a Go Slice and a Linked List, it is crucial to understand the underlying memory architecture.

### Memory Locality and Caching

* **Slices:** Go Slices are backed by arrays, which occupy contiguous blocks of memory. This allows the CPU to pre-fetch data efficiently, resulting in high cache hits and faster iteration.
* **Linked Lists:** Nodes are individually allocated on the heap. They are often scattered across memory addresses, leading to higher CPU cache misses during traversal.

### Garbage Collection (GC)

* **Slices:** A slice creates minimal pressure on the GC, as it is often treated as a single allocation block.
* **Linked Lists:** A list with 1,000 nodes requires 1,000 distinct allocations. This significantly increases the workload for the Go Garbage Collector during the mark-and-sweep phase.

### Comparison Table

| Feature | Go Slice (`[]T`) | Singly Linked List | Doubly Linked List |
| --- | --- | --- | --- |
| **Access Time** | $`O(1)`$ | $`O(N)`$ | $`O(N)`$ |
| **Head Insert/Delete** | $`O(N)`$ | $`O(1)`$ | $`O(1)`$ |
| **Tail Insert** | $`O(1)`$ (amortized) | $`O(1)`$ | $`O(1)`$ |
| **Middle Delete** | $`O(N)`$ | $`O(N)`$ | $`O(1)`$ (if node known) |
| **Memory Overhead** | Low | Medium (1 ptr) | High (2 ptrs) |

### Conclusion

For general-purpose collections in Go, the native Slice is almost always the preferred choice due to memory locality and syntax simplicity. However, Linked Lists are indispensable when the application requires  insertions and deletions from the beginning or middle of the collection, or when implementing specific structures like Graphs, Trees, or LRU Caches.
