# 269. Alien Dictionary

### Problem Link
[https://leetcode.com/problems/alien-dictionary/](https://leetcode.com/problems/alien-dictionary/)

### Description
There is a new alien language that uses the Latin alphabet. However, the order among letters is unknown to you. You receive a list of non-empty words from the dictionary, where words are sorted lexicographically by the rules of this new language. Derive the order of letters in this language.

**Example 1:**
```
Input: ["wrt", "wrf", "er", "ett", "rftt"]
Output: "wertf"
```

**Example 2:**
```
Input: ["z", "x"]
Output: "zx"
```

**Example 3:**
```
Input: ["z", "x", "z"]
Output: ""
Explanation: The order is invalid, so return "".
```

**Constraints:**
- You may assume all letters are in lowercase.
- If `a` is a prefix of `b` (e.g., "apple" and "app"), then `b` must not appear before `a` in the dictionary. This is an invalid ordering.
- If the order is invalid, return an empty string.
- There may be multiple valid orders of letters; any one of them is a valid answer.

---

### Brute Force Solution

A true brute force solution would be to generate all possible permutations of the alphabet characters present in the words. For each permutation, we would then verify if it's a valid ordering by checking if the given list of words is sorted according to that permutation.

**Algorithm:**
1.  Find all unique characters across all words.
2.  Generate every possible permutation of these unique characters.
3.  For each permutation (which represents a potential alien alphabet order):
    -   Check if the input `words` array is lexicographically sorted according to this permutation.
    -   If it is, we have found a valid order, so return it.
4.  If no valid order is found after checking all permutations, the order is invalid, so return an empty string.

**Why it's not feasible:**
The number of permutations for `k` unique characters is `k!`. With up to 26 letters in the alphabet, this approach is computationally explosive (e.g., `26!` is a massive number) and not practical for solving the problem. It's a theoretical approach that highlights the need for a more structured, graph-based solution.

---

### Optimized Solution and Concepts

This problem can be modeled as finding a **topological sort** of a directed graph. The letters of the alphabet are the nodes of the graph, and a directed edge `u -> v` signifies that letter `u` must come before letter `v` in the alien alphabet.

**Algorithm (Kahn's Algorithm for Topological Sort):**

1.  **Build the Graph and In-degrees:**
    -   Create an adjacency list (`adjList`) to represent the graph and an `inDegrees` map to store the number of incoming edges for each character (node).
    -   Initialize the graph and in-degrees for all unique characters present in the words.
    -   Derive ordering rules by comparing adjacent words (`word1`, `word2`) in the input list. Find the first character where they differ. If `word1[i]` is `u` and `word2[i]` is `v`, it implies `u` comes before `v`. Add a directed edge `u -> v` to the graph and increment the in-degree of `v`.
    -   **Edge case:** If `word2` is a prefix of `word1` (e.g., `["abc", "ab"]`), the order is invalid. Return `""`.

2.  **Initialize the Queue:**
    -   Find all characters with an in-degree of `0`. These are the characters that can appear first in the ordering.
    -   Add all these characters to a queue.

3.  **Perform Topological Sort:**
    -   Initialize an empty string or list to store the final sorted order.
    -   While the queue is not empty:
        -   Dequeue a character `u`. Append it to the result.
        -   For each neighbor `v` of `u` (i.e., for each character that `u` points to):
            -   Decrement the in-degree of `v`.
            -   If the in-degree of `v` becomes `0`, it means all its prerequisites are met. Enqueue `v`.

4.  **Check for Cycles:**
    -   After the loop, if the length of the resulting sorted order is equal to the total number of unique characters, a valid topological sort was found. Return the result.
    -   If the lengths do not match, it means there was a cycle in the graph (e.g., `a -> b` and `b -> a`), which makes a valid ordering impossible. Return `""`.

**Time & Space Complexity:**
*   **Time Complexity:** `O(C)`, where `C` is the total number of characters in all words combined. This is because we iterate through all characters to build the graph. The topological sort part takes `O(V + E)`, where `V` is the number of unique characters (at most 26) and `E` is the number of ordering rules (at most `V^2`).
*   **Space Complexity:** `O(V + E)` or simply `O(1)` because the number of vertices and edges is bounded by the fixed-size alphabet (26 letters). The space is for the adjacency list, in-degree map, and queue.

---

### Reasoning to derive Optimized Solution from Brute Force

The brute force approach is impractical because it explores the entire solution space of permutations without any guidance. The key insight for optimization is that the problem isn't about finding *any* permutation but one that satisfies a set of specific ordering constraints.

The lexicographical sorting of the words provides these constraints directly. For example, if `["wrt", "wrf"]` is a sorted pair, we learn that `'t'` must come before `'f'`. This is a dependency or prerequisite relationship. Such relationships are naturally represented by a directed graph, where an edge `u -> v` means `u` must be processed before `v`.

Once the problem is framed as a graph, finding a valid alphabet order is equivalent to finding a linear ordering of its nodes that respects the directed edges. This is precisely the definition of a **topological sort**. This graph-based approach is far more efficient because it directly uses the constraints given in the input to construct the solution, rather than blindly guessing and checking.

---

### Edge Cases
*   **Invalid Order (Cycle):** The input implies a contradiction, like `z -> x` and `x -> z`. The topological sort will detect this because not all nodes will be visited (some nodes will never reach an in-degree of 0).
*   **Invalid Order (Prefix):** A word appears after its own prefix, e.g., `["apple", "app"]`. This is an invalid order and should be checked for when building the graph.
*   **Disconnected Graph:** Not all letters have ordering constraints relative to each other (e.g., `["a"], ["b"]`). The topological sort handles this correctly. The relative order of `a` and `b` in the output doesn't matter.
*   **Empty Input:** An empty list of words. The result should be an empty string.
*   **Single Word Input:** A single word gives no ordering information, but the output should contain all its unique characters in any order.

---

### Follow-up Questions and Answers

**Q: Why does the length of the result string tell us if there's a cycle?**

**A:** In a directed acyclic graph (DAG), a topological sort will include every single node. However, if a cycle exists, the nodes within that cycle (and any nodes that depend on them) will never have their in-degrees reduced to zero. For example, if `a -> b` and `b -> a`, `a` is waiting for `b` and `b` is waiting for `a`. Neither can be added to the queue after the initial set of nodes is processed. As a result, the final sorted list will be missing all the nodes involved in the cycle, making its length less than the total number of unique characters.

**Q: The problem says to return *any* valid order. How does the solution handle multiple valid orders?**

**A:** The specific order returned by Kahn's algorithm depends on the order in which nodes with an in-degree of zero are processed from the queue. If at any point the queue contains multiple nodes (e.g., both `'a'` and `'b'` have an in-degree of 0), the one dequeued first will appear earlier in the result. The provided Go solution uses a slice as a queue and sorts the initial zero-degree nodes to ensure a stable, predictable output for testing, but any of the possible processing orders would yield a valid result.
