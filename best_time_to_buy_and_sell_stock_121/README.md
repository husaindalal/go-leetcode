# 121. Best Time to Buy and Sell Stock

### Problem Link
[https://leetcode.com/problems/best-time-to-buy-and-sell-stock/](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

### Description
You are given an array `prices` where `prices[i]` is the price of a given stock on the `i`-th day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return `0`.

**Example 1:**
```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

**Example 2:**
```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e., max profit = 0.
```

---

### Brute Force Solution

The most straightforward approach is to consider every possible pair of buy and sell days. We can iterate through all possible days to buy the stock and for each buy day, iterate through all subsequent days to sell the stock.

**Algorithm:**
1. Initialize a `maxProfit` variable to `0`.
2. Use a nested loop:
   - The outer loop iterates from the first day to the second-to-last day, representing the `buyDay`.
   - The inner loop iterates from `buyDay + 1` to the last day, representing the `sellDay`.
3. For each pair of `(buyDay, sellDay)`, calculate the `profit = prices[sellDay] - prices[buyDay]`.
4. If this `profit` is greater than the current `maxProfit`, update `maxProfit`.
5. After checking all pairs, `maxProfit` will hold the maximum possible profit.

**Time & Space Complexity:**
*   **Time Complexity:** `O(n^2)`, where `n` is the number of days (the length of the `prices` array). The nested loops lead to a quadratic runtime.
*   **Space Complexity:** `O(1)`, as we only use a few variables to store the state.

---

### Optimized Solution and Concepts

We can solve this problem in a single pass by iterating through the prices and keeping track of two key pieces of information:
1. The lowest stock price encountered so far (`minPrice`).
2. The maximum profit seen so far (`maxProfit`).

This is a classic "Kadane's Algorithm" variation. As we iterate through the prices, we update our `minPrice` if we find a new low. For each price, we also calculate the potential profit if we were to sell on that day (i.e., `currentPrice - minPrice`). We then update our `maxProfit` if this potential profit is higher than what we've recorded.

**Algorithm:**
1. Initialize `minPrice` to a very large value (or the first price).
2. Initialize `maxProfit` to `0`.
3. Iterate through the `prices` array from the first to the last day:
   - At each price `p`:
     - If `p` is less than `minPrice`, update `minPrice = p`.
     - Otherwise, calculate the potential profit `p - minPrice`. If this is greater than `maxProfit`, update `maxProfit`.
4. Return `maxProfit`.

**Time & Space Complexity:**
*   **Time Complexity:** `O(n)`, because we only iterate through the array once.
*   **Space Complexity:** `O(1)`, as we only use a couple of variables to store `minPrice` and `maxProfit`.

---

### Reasoning to derive Optimized Solution from Brute Force

The brute force `O(n^2)` solution is inefficient because it recalculates the same information repeatedly. For each potential `buyDay`, it scans the rest of the array to find the best `sellDay`.

The key insight for optimization is that to maximize profit for a given `sellDay`, we only need to know one thing: the absolute minimum price that occurred *before* that day. We don't need to re-check every previous day.

By iterating through the array once, we can maintain a running `minPrice`. When we are at day `i`, `minPrice` holds the minimum price from day `0` to `i-1`. This allows us to instantly calculate the maximum possible profit if we sell on day `i`. This "one-pass" approach avoids the redundant work of the nested loops, reducing the time complexity from `O(n^2)` to a much more efficient `O(n)`.

---

### Edge Cases
*   **Prices are strictly decreasing:** `[5, 4, 3, 2, 1]`. The `maxProfit` will remain `0` as no profitable transaction is possible.
*   **Prices are all the same:** `[5, 5, 5, 5]`. The `maxProfit` will be `0`.
*   **Empty or single-element array:** `[]` or `[5]`. No transaction is possible, so the profit is `0`. The code should handle this without errors.
*   **The minimum price appears after the maximum price:** `[7, 8, 1, 5]`. The algorithm correctly finds the profit from `1` to `5` (`4`) and ignores the higher but earlier price of `8`.

---

### Hints / Cheat Sheet

*   **Brute Force**: Nested loops to check every `buy-sell` pair. `O(n^2)`.
*   **Optimized**: Single pass. Keep track of the minimum price seen so far and the max profit. `O(n)`. This is the way to go.

| Solution                 | Time Complexity | Space Complexity |
| ------------------------ | --------------- | ---------------- |
| Brute Force (Nested Loops)| `O(n^2)`        | `O(1)`           |
| Optimized (One Pass)     | `O(n)`          | `O(1)`           |

---

### Follow-up Questions and Answers

**Q: What if you were allowed to complete multiple transactions?**

**A:** This changes the problem to "Best Time to Buy and Sell Stock II" (LeetCode #122). In that case, you can simply accumulate all positive price changes. You can iterate through the prices and whenever `prices[i] > prices[i-1]`, you add the difference `prices[i] - prices[i-1]` to your total profit. This is equivalent to buying and selling on consecutive days whenever there's a profit to be made.

**Q: What if you were allowed at most *two* transactions?**

**A:** This is "Best Time to Buy and Sell Stock III" (LeetCode #123), a much harder dynamic programming problem. You would typically use DP states to track the maximum profit after buying once, selling once, buying twice, and selling twice. A common approach is to calculate the max profit for one transaction ending at or before day `i` (let's call this `left[i]`) and the max profit for one transaction starting at or after day `i` (let's call this `right[i]`). The max profit for two transactions would then be the maximum of `left[i] + right[i]` over all `i`.
