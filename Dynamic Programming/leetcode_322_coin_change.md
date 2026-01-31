# 🪙 LeetCode 322 – Coin Change

## Problem Summary

Given a list of coin denominations `coins` and an integer `amount`, return the minimum number of coins needed to make up that amount. Return `-1` if it’s not possible.

Constraints:
- You can use each coin unlimited times
- `coins` are positive integers

**Example:**
```text
coins = [1,2,5], amount = 11
Output: 3  (11 = 5 + 5 + 1)

coins = [2], amount = 3
Output: -1
```

---

## Core Difficulty

- Determine minimum coins efficiently
- Understand overlapping subproblems → suitable for DP or BFS
- Handle impossible cases (`-1`) and large `amount`
- Optional: optimize space from O(amount) → O(1)

---

## Solution 1: Dynamic Programming (Bottom-Up)

```python
from typing import List

class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        # Initialize DP array
        dp = [amount + 1] * (amount + 1)
        dp[0] = 0  # 0 coins needed for amount 0

        for n in range(1, amount + 1):
            for coin in coins:
                if n >= coin:
                    dp[n] = min(dp[n], dp[n - coin] + 1)

        return -1 if dp[amount] == amount + 1 else dp[amount]
```

### Complexity Analysis

- **Time:** O(amount × len(coins))
- **Space:** O(amount)
- Can reduce space to O(1) using rolling variables if needed

---

## Solution 2: BFS (Level-Order Search)

```python
from collections import deque

class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        if amount == 0:
            return 0

        queue = deque([0])
        visited = set([0])
        steps = 0

        while queue:
            steps += 1
            for _ in range(len(queue)):
                curr = queue.popleft()
                for coin in coins:
                    nxt = curr + coin
                    if nxt == amount:
                        return steps
                    if nxt < amount and nxt not in visited:
                        visited.add(nxt)
                        queue.append(nxt)
        return -1
```

### Complexity Analysis

- **Time:** O(amount × len(coins)) (worst case)
- **Space:** O(amount) (queue + visited set)

---

## Solution 3: DFS + Backtracking with Pruning

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        coins.sort(reverse=True)  # try larger coins first
        self.ans = float('inf')

        def dfs(index, remain, count):
            if remain == 0:
                self.ans = min(self.ans, count)
                return
            if index == len(coins):
                return

            max_use = remain // coins[index]
            for k in range(max_use, -1, -1):
                if count + k >= self.ans:
                    break  # prune
                dfs(index + 1, remain - k * coins[index], count + k)

        dfs(0, amount, 0)
        return -1 if self.ans == float('inf') else self.ans
```

---

## Interview Talking Points

- DP vs BFS vs DFS: show understanding of overlapping subproblems and optimization
- Edge cases:
  - `amount = 0` → 0 coins
  - Impossible amounts → return -1
- Space optimization: DP array → rolling variables
- Greedy does **not always work** for arbitrary coin sets
  - Example: `coins = [1,3,4], amount = 6` → greedy fails

---

## Key Takeaways

- Recognize **subproblem structure**
- Use **DP, BFS, or DFS + memo/pruning**
- Handle **edge cases** carefully
- Space optimization can be discussed in interviews

