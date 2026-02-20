
# LeetCode 474 – Ones and Zeroes

## Problem Summary

You are given an array of binary strings `strs` and two integers `m` and `n`.

Return the size of the largest subset of `strs` such that:
- At most `m` zeros
- At most `n` ones

---

# Core Insight

This is a classic:

- 0/1 Knapsack problem
- 2D Knapsack (two constraints: zeros and ones)
- State DP problem

Each string is an item.
Weight 1 = number of zeros
Weight 2 = number of ones
Value = 1 (we count how many strings we take)

---

# State Definition

dp[i][j] = maximum number of strings we can form
            using at most i zeros and j ones

---

# 2D 0/1 Knapsack Solution

## Key Idea

For each string:
- Count zeros and ones
- Iterate backwards (because 0/1 knapsack)

---

## Code

```python
class Solution:
    def findMaxForm(self, strs, m, n):
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for s in strs:
            zeros = s.count('0')
            ones = s.count('1')

            for i in range(m, zeros - 1, -1):
                for j in range(n, ones - 1, -1):
                    dp[i][j] = max(dp[i][j],
                                   dp[i - zeros][j - ones] + 1)

        return dp[m][n]
```

Time Complexity: O(len(strs) * m * n)
Space Complexity: O(m * n)

---

# Why Iterate Backward?

Because this is 0/1 knapsack.

Backward iteration ensures:
Each string is used at most once.

If iterated forward → becomes complete knapsack (can reuse item).

---

# Alternative 3D DP (Educational Only)

State:

dp[k][i][j] =
Using first k strings,
max strings with i zeros and j ones.

Transition:

Take or not take current string.

But space = O(len(strs) * m * n) → too large.

So we compress to 2D.

---

# Interview Discussion Points

1. Why is this 0/1 knapsack?
2. Why is it 2D knapsack?
3. Why iterate backward?
4. What if there were 3 constraints?
5. Can we reduce to 1D? (No, because 2 independent capacities)

---

# Connection to Other Problems

- 416 Partition Equal Subset Sum → 1D 0/1 knapsack
- 494 Target Sum → count knapsack
- 322 Coin Change → complete knapsack
- 474 → 2D 0/1 knapsack

---

# Key Takeaways

- Multi-dimensional knapsack pattern
- Backward iteration is critical
- State compression reduces 3D → 2D
- Classic medium-hard DP interview problem
