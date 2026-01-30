# LeetCode 91 – Decode Ways

## Problem Summary

Given a string `s` containing digits, return the number of ways to decode it into letters ('A'=1, 'B'=2, ..., 'Z'=26').

Constraints:
- Leading zeros are invalid
- Only '1'-'26' are valid codes

---

## Core Difficulty

- Recognize valid single-digit and two-digit decodings
- Efficient recursion with memoization
- DP table indexing and base case handling

---

## Solution 1: Dynamic Programming (Bottom-Up)

```python
class Solution:
    def numDecodings(self, s: str) -> int:
        if not s or s[0] == '0':
            return 0

        n = len(s)
        dp = [0] * (n + 1)
        dp[0] = 1  # empty string has 1 way
        dp[1] = 1  # first character is valid (not '0')

        for i in range(2, n + 1):
            # single-digit decoding
            if int(s[i-1:i]) > 0:
                dp[i] += dp[i-1]
            # two-digit decoding
            if 10 <= int(s[i-2:i]) <= 26:
                dp[i] += dp[i-2]

        return dp[n]
```

### Complexity Analysis

- Time: O(n)
- Space: O(n) (can be reduced to O(1) with two variables)

---

## Solution 2: Top-Down DFS with Memoization (Python @cache)

```python
from functools import cache

class Solution:
    def numDecodings(self, s: str) -> int:
        n = len(s)

        @cache
        def dfs(index: int) -> int:
            if index == n:
                return 1  # successfully decoded to the end
            if s[index] == '0':
                return 0  # cannot start with '0'

            # 1-step decoding
            res = dfs(index + 1)

            # 2-step decoding
            if index + 1 < n and (s[index] == '1' or (s[index] == '2' and s[index+1] <= '6')):
                res += dfs(index + 2)

            return res

        return dfs(0)
```

- Uses Python's `@cache` for memoization
- Avoids manually maintaining a memo array

---

## Solution 3: Top-Down DFS with Explicit Memo Array

```python
class Solution:
    def numDecodings(self, s: str) -> int:
        n = len(s)
        memo = [-1] * n

        def dfs(index: int) -> int:
            if index == n:
                return 1
            if s[index] == '0':
                return 0
            if memo[index] != -1:
                return memo[index]

            step1 = dfs(index + 1)
            step2 = 0
            if index + 1 < n and (s[index] == '1' or (s[index] == '2' and s[index+1] <= '6')):
                step2 = dfs(index + 2)

            memo[index] = step1 + step2
            return memo[index]

        return dfs(0)
```

- Same logic as DFS with cache
- Manual memo array allows better control in interviews

---

## Interview Talking Points

- DP vs DFS + memo: both are valid, show understanding of overlapping subproblems
- Edge cases: empty string, leading '0', single character
- Optimization: reduce DP array to two variables for O(1) space
- Can discuss extension to variable-length encoding or multiple alphabets

---

## Key Takeaways

- Recognize **one-step vs two-step decoding**
- Use **memoization** to avoid recomputation
- Base cases and invalid conditions are critical
- Space can be optimized using rolling variables

