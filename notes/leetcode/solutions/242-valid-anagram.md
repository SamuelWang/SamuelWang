# Algorithm Deep Dive: Valid Anagram in Go

The "Valid Anagram" problem (LeetCode 242) is a common algorithmic challenge that serves as an excellent introduction to string manipulation and hash table usage. While the concept is straightforward, writing a production-grade solution requires understanding character encoding and memory optimization.

## Problem Statement

Given two strings, `s` and `t`, write a function to return `true` if `t` is an anagram of `s`, and `false` otherwise.

**Example 1:**

* Input: `s = "anagram"`, `t = "nagaram"`
* Output: `true`

**Example 2:**

* Input: `s = "rat"`, `t = "car"`
* Output: `false`

## Core Logic: Frequency Counting

An anagram is defined by having the exact same characters with the exact same frequencies as the original string, just in a different order. Therefore, the most efficient way to solve this is not by sorting the strings (which would take $`O(N \log N)`$ time), but by counting the occurrences of each character.

The general algorithm follows these steps:

1. **Length Check:** If the length of `s` does not equal the length of `t`, they cannot be anagrams. This acts as an immediate failure condition.
2. **Count:** Iterate through the first string and increment the count for each character.
3. **Subtract:** Iterate through the second string and decrement the count for each character.
4. **Verify:** Check if all counts return to zero.

## Solution 1: Fixed-Size Array (Optimized)

For the standard LeetCode constraint where strings consist only of lowercase English letters, a fixed-size integer array is the most performant data structure. It avoids the overhead of a hash map and resides in stack memory.

In Go, we use an array of size 26 (`[26]int`). We map characters to indices by subtracting the rune `'a'`. For example, `'a' - 'a'` results in index `0`, while `'z' - 'a'` results in index `25`.

```go
func isAnagram(s string, t string) bool {
    // 1. Fail-fast optimization
    if len(s) != len(t) {
        return false
    }

    // 2. Initialize fixed-size array
    // Go arrays default to 0 values
    var charCount [26]int

    // 3. Single pass iteration (since lengths are equal)
    for i := 0; i < len(s); i++ {
        charCount[s[i]-'a']++
        charCount[t[i]-'a']--
    }

    // 4. Verification pass
    for _, count := range charCount {
        if count != 0 {
            return false
        }
    }

    return true
}

```

### Complexity Analysis

* **Time Complexity:** $`O(N)`$, where $`N`$ is the length of the string. We iterate through the strings once and then iterate through the constant-size array.
* **Space Complexity:** $`O(1)`$. The array size is constant (26) regardless of the input string size.

## Solution 2: Hash Map (Unicode Compatible)

In real-world software development, assuming input is restricted to "lowercase English letters" is often dangerous. If the input contains Unicode characters (e.g., emojis, Mandarin, or accented letters), the array approach will panic or fail.

To handle Unicode in Go, we utilize a `map[rune]int`. Go's `range` loop over a string automatically decodes UTF-8 bytes into runes.

```go
func isAnagramUnicode(s string, t string) bool {
    if len(s) != len(t) {
        return false
    }

    // Map keys are runes (Unicode code points)
    counts := make(map[rune]int)

    for _, char := range s {
        counts[char]++
    }

    for _, char := range t {
        counts[char]--
        // Optimization: if count drops below zero, t has an extra char
        if counts[char] < 0 {
            return false
        }
    }

    return true
}

```

### Complexity Analysis

* **Time Complexity:** $`O(N)`$.
* **Space Complexity:** $`O(K)`$, where $`K`$ is the number of unique characters in the string. In the worst case, this approaches $`O(N)`$.

## Summary

When solving Valid Anagram in Go, the choice of implementation depends on the constraints:

1. **Competitive Programming/LeetCode:** Use the **Fixed-Size Array** approach. It is faster and uses less memory because it avoids hash collisions and dynamic allocation.
2. **Production Systems:** Use the **Hash Map** approach. It is robust against varied inputs and internationalization requirements.
