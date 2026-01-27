# Understanding Valid Parentheses: A Golang Approach

The "Valid Parentheses" problem (LeetCode 20) is a classic algorithmic challenge that tests a developer's understanding of data structures—specifically the Stack. While it is a common interview question, the logic behind it is fundamental to how compilers, IDEs, and data parsers (like JSON or XML) interpret code structure.

## The Problem Statement

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, the goal is to determine if the input string is valid.

A string is considered valid if:

1. Open brackets are closed by the same type of brackets.
2. Open brackets are closed in the correct order.

**Examples:**

* `"()"`  `true`
* `"()[]{}"`  `true`
* `"(]"`  `false`
* `"([)]"`  `false` (Incorrect nesting order)

## The Solution: Using a Stack

The core difficulty of this problem is managing nested scopes. When an opening bracket appears, we enter a new context that must be resolved before returning to the previous one. This "Last-In, First-Out" (LIFO) requirement makes the **Stack** the ideal data structure.

### The Algorithm

1. **Early Exit:** If the string length is odd, it cannot be valid (every opener needs a closer). Return `false` immediately.
2. **Initialize:** Create an empty stack (in Go, a slice serves this purpose).
3. **Traverse:** Iterate through the string character by character.
4. **Push Openers:** If the character is an opening bracket (`(`, `{`, `[`), push it onto the stack.
5. **Match Closers:** If the character is a closing bracket (`)`, `}`, `]`):
* Check if the stack is empty. If it is, the string is invalid (closing bracket with no opener).
* Check the top element of the stack. If it matches the corresponding opening bracket, **pop** it off the stack.
* If it does not match, the string is invalid.


6. **Final Validation:** After the loop finishes, check if the stack is empty. If elements remain, it means there are unclosed brackets.

## Go Implementation

In Go, we do not need a specialized Stack library. A standard slice of `runes` functions efficiently as a stack using `append` for push operations and slice indexing for pop operations.

```go
package main

import "fmt"

func isValid(s string) bool {
    // Optimization: An odd-length string implies a missing pair
    if len(s)%2 != 0 {
        return false
    }

    // Map to store closing-to-opening bracket pairs for O(1) lookup
    // Key: Closing Bracket, Value: Opening Bracket
    pairs := map[rune]rune{
        ')': '(',
        ']': '[',
        '}': '{',
    }

    // Initialize the stack using a slice of runes
    stack := []rune{}

    for _, char := range s {
        // Check if the current char is a closing bracket
        expectedOpen, isClosing := pairs[char]
        
        if isClosing {
            // Case 1: Stack is empty but we found a closing bracket
            if len(stack) == 0 {
                return false
            }

            // Case 2: Check the top of the stack
            topIndex := len(stack) - 1
            top := stack[topIndex]

            // If the top doesn't match the expected opener, return false
            if top != expectedOpen {
                return false
            }

            // Valid match: Pop the element from the stack
            stack = stack[:topIndex]
        } else {
            // It is an opening bracket; push it onto the stack
            stack = append(stack, char)
        }
    }

    // If the stack is empty, all brackets were matched and closed
    return len(stack) == 0
}

```

## Complexity Analysis

### Time Complexity: $`O(n)`$

The algorithm iterates through the input string exactly once.

* **Push operations:** In Go, `append` is amortized $`O(1)`$.
* **Pop operations:** Reslicing a slice is $`O(1)`$.
* **Map lookups:** Accessing the `pairs` map is $`O(1)`$.

Therefore, the time complexity is linear relative to the size of the input string .

### Space Complexity: $`O(n)`$

In the worst-case scenario, the string consists entirely of opening brackets (e.g., `"((((("`). In this case, we push every character onto the stack. Therefore, the memory usage grows linearly with the input size.

## Key Takeaways

* **Rune vs. Byte:** The solution uses `rune` to handle characters. While the problem constraint usually implies ASCII, using `rune` makes the solution safe for Unicode strings.
* **Map for Readability:** Using a map to define pairs keeps the code clean. For extreme performance-critical paths, a `switch` statement might offer a negligible speed improvement by avoiding hashing, but the map is generally preferred for maintainability.
* **State Management:** This problem illustrates how Stacks are used to maintain state in parsing logic, ensuring that inner scopes are closed before outer scopes can be completed.
