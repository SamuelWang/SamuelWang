# Implementing the Stack Data Structure in Go (Golang)

In the Go programming language, the standard library does not provide a dedicated `Stack` class or type. This is a deliberate design choice, as Go encourages developers to build data structures using simple primitives like Slices or Linked Lists.

This article outlines how to implement a Stack in Go using modern practices, specifically leveraging **Generics** (introduced in Go 1.18) to ensure type safety and reusability.

## The Concept: LIFO

A Stack operates on the **Last-In, First-Out (LIFO)** principle. The most recently added element is the first one to be removed. The core operations are:

* **Push:** Add an element to the top of the stack.
* **Pop:** Remove and return the top element.
* **Peek:** Return the top element without removing it.

## Implementation A: The Slice-Based Stack

For the majority of use cases in Go, a slice is the most efficient underlying structure. Slices utilize contiguous memory blocks, which provides excellent cache locality and reduces the overhead on the Garbage Collector compared to pointer-heavy structures like linked lists.

Below is a generic implementation using a slice:

```go
package main

import (
	"errors"
	"fmt"
)

// Stack represents a generic stack using a slice.
type Stack[T any] struct {
	elements []T
}

// Push adds a value to the top of the stack.
// Complexity: Amortized O(1)
func (s *Stack[T]) Push(value T) {
	s.elements = append(s.elements, value)
}

// Pop removes and returns the top value.
// Complexity: O(1)
func (s *Stack[T]) Pop() (T, error) {
	if s.IsEmpty() {
		var zero T
		return zero, errors.New("stack is empty")
	}
	
	// Get the last index
	index := len(s.elements) - 1
	element := s.elements[index]
	
	// Remove the element from the slice by slicing it off.
	// Important: If T is a pointer, set s.elements[index] = nil 
	// before shrinking to avoid memory leaks.
	s.elements = s.elements[:index]
	
	return element, nil
}

// Peek returns the top value without removing it.
// Complexity: O(1)
func (s *Stack[T]) Peek() (T, error) {
	if s.IsEmpty() {
		var zero T
		return zero, errors.New("stack is empty")
	}
	return s.elements[len(s.elements)-1], nil
}

// IsEmpty checks if the stack has no elements.
func (s *Stack[T]) IsEmpty() bool {
	return len(s.elements) == 0
}

// Size returns the number of elements.
func (s *Stack[T]) Size() int {
	return len(s.elements)
}

```

### Note on Memory Management

When using a slice-based stack with pointer types, simply reducing the slice length (e.g., `s.elements[:index]`) does not immediately remove the reference to the underlying object in the backing array. To ensure the Garbage Collector can reclaim the memory, explicitly zero out the element before shrinking the slice.

## Implementation B: The Linked List Stack

A Linked List implementation creates a new node for every element pushed onto the stack. This approach is useful when memory fragmentation is a concern or when strict $`O(1)`$ worst-case performance is required for `Push` operations (avoiding the occasional resize cost of a slice). However, this method incurs higher memory overhead per element due to next-pointers.

```go
// Node represents an element in the linked list.
type Node[T any] struct {
	Value T
	Next  *Node[T]
}

// LinkedStack represents a stack implemented as a linked list.
type LinkedStack[T any] struct {
	top  *Node[T]
	size int
}

// Push adds a new node to the top.
// Complexity: Strict O(1)
func (s *LinkedStack[T]) Push(value T) {
	newNode := &Node[T]{Value: value, Next: s.top}
	s.top = newNode
	s.size++
}

// Pop removes the top node.
// Complexity: O(1)
func (s *LinkedStack[T]) Pop() (T, error) {
	if s.top == nil {
		var zero T
		return zero, errors.New("stack is empty")
	}
	value := s.top.Value
	s.top = s.top.Next
	s.size--
	return value, nil
}

```

## Performance Comparison

When choosing between these two implementations, consider the following trade-offs:

| Feature | Slice-Based (Vector) | Linked List-Based |
| --- | --- | --- |
| **Cache Locality** | **High** (Contiguous memory) | Low (Scattered heap allocations) |
| **Memory Overhead** | Low (Only slice header + capacity) | High (Extra pointer per element) |
| **Push Performance** | Amortized $`O(1)`$ (Occasional resize) | Strict $`O(1)`$ |
| **GC Pressure** | Low (Fewer allocations) | High (Allocation per `Push`) |

Generally, the **Slice-based** implementation is preferred in Go due to better cache locality and reduced pressure on the Garbage Collector.

## Thread Safety

The standard implementations above are **not** thread-safe. If multiple goroutines attempt to push or pop from the same stack instance concurrently, race conditions will occur.

To implement a thread-safe stack, synchronization primitives such as `sync.Mutex` or `sync.RWMutex` must be used.

```go
import "sync"

type ConcurrentStack[T any] struct {
	lock     sync.Mutex
	elements []T
}

func (s *ConcurrentStack[T]) Push(v T) {
	s.lock.Lock()
	defer s.lock.Unlock()
	s.elements = append(s.elements, v)
}

func (s *ConcurrentStack[T]) Pop() (T, error) {
	s.lock.Lock()
	defer s.lock.Unlock()
	
	if len(s.elements) == 0 {
		var zero T
		return zero, errors.New("stack is empty")
	}
	
	index := len(s.elements) - 1
	element := s.elements[index]
	s.elements = s.elements[:index]
	return element, nil
}

```

While locks ensure safety, they introduce latency. In high-performance concurrent systems, alternatives such as channel-based stacks or designing the system to avoid shared state (e.g., using local stacks per goroutine) are often explored.
