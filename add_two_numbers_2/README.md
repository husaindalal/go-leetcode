# 2. Add Two Numbers

### Problem Link
[https://leetcode.com/problems/add-two-numbers/](https://leetcode.com/problems/add-two-numbers/)

### Description
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in **reverse order**, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example:**
```
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
```

---

### Brute Force Solution

The brute force approach is to convert the linked lists into actual numbers, perform the addition, and then convert the result back into a linked list.

Since the numbers can be very large and exceed the limits of standard integer types, we should use a data type that can handle arbitrary-precision arithmetic, like `big.Int` in Go.

**Algorithm:**
1.  **Convert `l1` to a number:** Traverse the first linked list (`l1`) to build the first number. Since the digits are in reverse order, a recursive traversal to the end of the list allows us to build the number in the correct order. For `2 -> 4 -> 3`, the number is `342`.
2.  **Convert `l2` to a number:** Do the same for the second linked list (`l2`).
3.  **Sum the numbers:** Add the two numbers together.
4.  **Convert sum to a list:** Create a new linked list from the sum. This can be done by repeatedly taking the number modulo 10 to get the last digit, creating a new node for it, and then dividing the number by 10 to process the next digit. This naturally creates the new list with digits in the correct reverse order.

**Time & Space Complexity:**
*   **Time Complexity:** `O(max(N, M))`, where N and M are the lengths of the two lists. This is because we traverse each list once.
*   **Space Complexity:** `O(max(N, M))` to store the number as a string or in a `big.Int` and to build the resulting linked list. The recursion for the conversion also uses stack space.

---

### Optimized Solution and Concepts

A more direct and efficient solution is to simulate elementary school addition directly on the linked lists. We can iterate through both lists simultaneously, from head to tail (which corresponds to the least significant digit to the most significant), adding the digits at each position along with a `carry` from the previous position.

**Algorithm:**
1.  Initialize a dummy head for the result list and set a `carry` variable to `0`.
2.  Iterate as long as there are nodes in `l1`, `l2`, or there is a remaining `carry`.
3.  In each iteration, get the values from the current nodes of `l1` and `l2`. If a list has been fully traversed, its value for the current position is considered `0`.
4.  Calculate the `sum` of the two digits and the `carry`: `sum = l1.val + l2.val + carry`.
5.  The new `carry` for the next position is `sum / 10`.
6.  The digit for the new node in our result list is `sum % 10`.
7.  Create the new node with this digit and append it to the result list.
8.  Move to the next nodes in `l1` and `l2` if they exist.
9.  After the loop, the list starting from the dummy head's `next` pointer is the result.

This approach avoids the overhead of converting to and from large number representations and handles the addition in a single pass.

**Time & Space Complexity:**
*   **Time Complexity:** `O(max(N, M))`, as we traverse each list at most once.
*   **Space Complexity:** `O(max(N, M))` for the new linked list that stores the result.

---

### Reasoning to derive Optimized Solution from Brute Force

The brute force solution is functionally correct but involves multiple, separate steps: conversion from list to number for both inputs, a potentially heavy addition operation, and a final conversion from a number back to a list. This is indirect and less efficient than it could be.

The key insight for optimization is that the "add with carry" operation we learn in school works digit by digit. Since the linked lists provide the numbers digit by digit (already in the correct order for addition, from least to most significant), we can apply this logic directly.

By processing both lists in a single, simultaneous pass, we can calculate each digit of the sum and construct the result list at the same time. This eliminates the need for any intermediate data structures like `big.Int`, simplifying the logic, reducing memory overhead, and improving performance by combining all operations into one loop.

---

### Edge Cases
*   **One list is longer than the other:** The optimized solution handles this gracefully by treating a `nil` node as having a value of `0`.
*   **The sum results in an extra digit:** For example, `[9,9] + [1]` results in `[0,0,1]` (i.e., `99 + 1 = 100`). This is handled by continuing the loop as long as `carry > 0`, even after both lists are exhausted.
*   **Input lists containing the number 0:** e.g., `[0]`. The algorithm handles this correctly.
*   **Empty lists:** The problem statement specifies non-empty lists, but a robust solution should handle this, likely by returning the non-empty list or `nil` if both are empty.

---

### Hints / Cheat Sheet

*   **Brute Force**: Convert lists to `big.Int`, add, then convert the sum back to a list. Watch out for integer overflow if not using a big integer library.
*   **Optimized (Elementary Addition)**: The best way is to iterate through the lists together, as if you're doing addition on paper. Use a `carry` variable. Remember to handle the final carry-out. A dummy head node can simplify the code for building the new list.

| Solution                 | Time Complexity | Space Complexity |
| ------------------------ | --------------- | ---------------- |
| Brute Force (with BigInt)| `O(max(N,M))`   | `O(max(N,M))`   |
| Optimized (Iteration)    | `O(max(N,M))`   | `O(max(N,M))`   |

---

### Follow-up Questions and Answers

**Q: What if the digits were stored in forward order (e.g., `3 -> 4 -> 2` represents 342)?**

**A:** If the digits were in forward order, the "Elementary Addition" approach from head to tail would not work because we need to start from the least significant digit (which is now at the end of the list).

There are two main ways to solve this:
1.  **Reverse the Lists:** The simplest approach is to reverse both input linked lists first. After reversing, the problem becomes identical to the original, so we can apply the same optimized algorithm. Finally, the resulting list must be reversed to restore the correct forward order.
2.  **Use Stacks:** An alternative that avoids modifying the input lists is to use stacks. Push all digits of `l1` onto a stack, and all digits of `l2` onto another. Then, pop digits from the stacks one by one, add them with a carry, and create the new list. Each new node would be prepended to the result list, which is an `O(1)` operation. This way, the final list is built in the correct forward order.
