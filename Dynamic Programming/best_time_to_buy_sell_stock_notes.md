# 📈 LeetCode – Best Time to Buy and Sell Stock Series

## Problem 121 – Best Time to Buy and Sell Stock

### Problem Summary
Given an array `prices`, find the maximum profit by buying and selling **once**.

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        minVal = float('inf')
        res = 0
        for price in prices:
            res = max(res, price - minVal)
            minVal = min(minVal, price)
        return res
```

- Time: O(n), Space: O(1)
- Track minimum price so far and max profit

---

## Problem 122 – Best Time to Buy and Sell Stock II

### Problem Summary
Can buy and sell **multiple times** (any number of transactions).

**DP Approach:**
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        not_hold = 0
        hold = -prices[0]
        for price in prices[1:]:
            temp = not_hold
            not_hold = max(not_hold, hold + price)
            hold = max(temp - price, hold)
        return not_hold
```

**Greedy Approach:**
```python
class Solution:
    def maxProfit(self, prices):
        profit = 0
        for i in range(1, len(prices)):
            if prices[i] > prices[i - 1]:
                profit += prices[i] - prices[i - 1]
        return profit
```

- Time: O(n), Space: O(1)
- Greedy: sum of all upward slopes
- DP: explicit hold/not_hold states

---

## Problem 309 – Best Time to Buy and Sell Stock with Cooldown

- Similar to 122 but **after selling must cooldown one day**.

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        hold = -prices[0]
        not_hold = 0
        cooldown = 0

        for i in range(1, len(prices)):
            prev_hold = hold
            prev_not_hold = not_hold
            prev_cooldown = cooldown

            hold = max(prev_hold, not_hold - prices[i])
            not_hold = max(prev_not_hold, prev_cooldown)
            cooldown = prev_hold + prices[i]

        return max(not_hold, cooldown)
```

- States: hold, not_hold, cooldown
- DP with O(n) time and O(1) space

---

## Problem 714 – Best Time to Buy and Sell Stock with Transaction Fee

- Each sale has a fee

```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        hold = -prices[0]
        not_hold = 0
        for i in range(1, len(prices)):
            prev_not_hold = not_hold
            not_hold = max(prev_not_hold, hold + prices[i] - fee)
            hold = max(hold, prev_not_hold - prices[i])
        return not_hold
```

- Track hold / not_hold states
- Fee reduces profit when selling

---

## Problem 123 – Best Time to Buy and Sell Stock III

- Can make **at most two transactions**

```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        t1_cost = float('inf')
        t2_cost = float('inf')
        t1_profit = 0
        t2_profit = 0

        for price in prices:
            t1_cost = min(t1_cost, price)
            t1_profit = max(t1_profit, price - t1_cost)
            t2_cost = min(t2_cost, price - t1_profit)
            t2_profit = max(t2_profit, price - t2_cost)

        return t2_profit
```

- Track first and second transaction profits
- Time: O(n), Space: O(1)

---

## Problem 188 – Best Time to Buy and Sell Stock IV

- At most **k transactions**

```python
class Solution:
    def maxProfit(self, k: int, prices: List[int]) -> int:
        if k >= len(prices)//2:
            return sum(max(prices[i+1]-prices[i],0) for i in range(len(prices)-1))

        dp = [[0]*len(prices) for _ in range(k+1)]
        for i in range(1,k+1):
            max_diff = -prices[0]
            for j in range(1,len(prices)):
                dp[i][j] = max(dp[i][j-1], prices[j] + max_diff)
                max_diff = max(max_diff, dp[i-1][j] - prices[j])
        return dp[k][-1]
```

- Use DP table for k transactions
- Optimization for large k (convert to unlimited transaction)

---

## Problem 309, 714, 123, 188 – Summary

- Use **state DP** for cooldown / fee / limited transactions
- 121, 122 simple cases: greedy works
- 123, 188 track multiple transaction profits
- O(n) time often achievable with O(1) or O(k) space

---

## Interview Talking Points

- Difference between 121, 122, 123, 188, 309, 714
- When greedy works vs when DP state is needed
- Explain hold / not_hold / cooldown states
- Time-space trade-offs
- How to handle unlimited vs limited transactions

