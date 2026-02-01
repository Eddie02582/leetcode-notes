# 🔗 LeetCode 1143 – Longest Common Subsequence

## Problem Summary

Given two strings `text1` and `text2`, return the length of their longest common subsequence (LCS).

Constraints:
- Subsequence need not be contiguous
- Only order matters
- Return an integer

**Example:**
```text
text1 = "abcde", text2 = "ace"
Output: 3 (LCS = "ace")
```

---

## Core Difficulty

- Recognizing overlapping subproblems → DP
- Indexing correctly in 2D DP array
- Optional: optimize space to 1D

---

## Solution 1: Bottom-Up 2D DP (Your Approach)

```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m, n = len(text1), len(text2)
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if text1[i - 1] == text2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1] + 1
                else:
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
        return dp[m][n]
```

### Complexity
- Time: O(m * n)
- Space: O(m * n)

---

## Solution 2: Bottom-Up 1D DP (Space Optimized)

```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        if len(text1) < len(text2):
            text1, text2 = text2, text1  # Ensure text2 is shorter

        n = len(text2)
        prev = [0] * (n + 1)

        for c1 in text1:
            curr = [0] * (n + 1)
            for j, c2 in enumerate(text2, 1):
                if c1 == c2:
                    curr[j] = prev[j - 1] + 1
                else:
                    curr[j] = max(prev[j], curr[j - 1])
            prev = curr
        return prev[n]
```

- Reduces space from O(m * n) → O(n) while maintaining time O(m * n)

---

## Solution 3: Top-Down DFS + Memo

```python
from functools import lru_cache

class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m, n = len(text1), len(text2)

        @lru_cache(None)
        def dfs(i, j):
            if i == m or j == n:
                return 0
            if text1[i] == text2[j]:
                return 1 + dfs(i + 1, j + 1)
            return max(dfs(i + 1, j), dfs(i, j + 1))

        return dfs(0, 0)
```

- Intuitive recursive approach
- Memoization avoids recomputation
- Space complexity includes recursion stack and memo O(m * n)

---

## Interview Talking Points

- 2D DP array: classic bottom-up
- Space optimization to 1D
- Top-down recursion with memoization is easier to explain
- Base cases: dp[0][*] = dp[*][0] = 0
- Relation to other DP problems: edit distance, longest increasing subsequence

---

## Key Takeaways

- LCS is classic **2D DP problem**
- Bottom-up vs Top-down: tradeoff between memory and clarity
- Space can often be reduced using rolling arrays
- Memoization is essential for DFS

