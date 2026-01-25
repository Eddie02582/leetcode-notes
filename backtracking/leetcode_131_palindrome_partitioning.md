# LeetCode 131 – Palindrome Partitioning

## Problem Summary
Given a string `s`, partition `s` such that every substring of the partition is a **palindrome**. Return all possible palindrome partitioning of `s`.

---

## Core Difficulty
- Palindrome checking for substrings efficiently
- Backtracking with recursion
- Managing path state and recursion depth

---

## Key Insight
- Use **dynamic programming** to precompute whether any substring `s[i:j]` is a palindrome.
- Use **backtracking** to explore all possible partition positions.
- Append the current path when reaching the end of the string.

---

## Dynamic Programming – Palindrome Table
```python
n = len(s)
dp = [[False]*n for _ in range(n)]

for i in range(n-1,-1,-1):
    for j in range(i,n):
        if s[i] == s[j]:
            dp[i][j] = j - i <= 2 or dp[i+1][j-1]
```
- `dp[i][j]` is True if `s[i:j+1]` is a palindrome
- Check from end to start to ensure `dp[i+1][j-1]` is already computed
- `j - i <= 2` handles substrings of length 1, 2, or 3

---

## Backtracking Framework
```python
res = []

def backtracking(idx, path):
    if idx == n:
        res.append(path[:])
        return

    for j in range(idx, n):
        if dp[idx][j]:
            path.append(s[idx:j+1])
            backtracking(j+1, path)
            path.pop()

backtracking(0, [])
```
- `idx` tracks current position in string
- `path` stores the current partition
- Append `path[:]` to `res` when reaching the end
- Use `dp` to skip non-palindrome cuts

---

## Full Solution
```python
from typing import List

class Solution:
    def partition(self, s: str) -> List[List[str]]:
        n = len(s)
        dp = [[False]*n for _ in range(n)]

        # Build palindrome DP table
        for i in range(n-1, -1, -1):
            for j in range(i, n):
                if s[i] == s[j]:
                    dp[i][j] = j - i <= 2 or dp[i+1][j-1]

        res = []
        
        def backtracking(idx, path):
            if idx == n:
                res.append(path[:])
                return

            for j in range(idx, n):
                if dp[idx][j]:
                    path.append(s[idx:j+1])
                    backtracking(j+1, path)
                    path.pop()

        backtracking(0, [])
        return res
```

---

## Complexity Analysis
- **Time:** `O(n*2^n)` — each substring is checked via DP in `O(1)`; backtracking explores all partitions
- **Space:** `O(n^2)` for DP table + `O(n)` recursion depth + `O(2^n)` result storage

---

## Interview Talking Points
- Use DP table to check palindrome efficiently
- Backtracking template for partitioning problems
- Avoid recomputing palindrome checks
- Discuss pruning possibilities (skip non-palindromes early)

---

## Key Takeaways
- Combining **DP + Backtracking** solves palindrome partitioning efficiently
- Template applies to other substring partitioning problems
- Preprocessing with DP improves backtracking performance

