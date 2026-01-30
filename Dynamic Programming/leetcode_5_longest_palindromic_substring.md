# LeetCode 5 – Longest Palindromic Substring

## Problem Summary

Given a string `s`, find the **longest palindromic substring** in `s`.

---

## Core Difficulty

- Recognize palindromic substrings
- Efficiently find the longest without checking all O(n^2) substrings naively
- Dynamic programming vs expand-around-center approaches

---

## Solution 1: Dynamic Programming (DP)

- `dp[i][j]` indicates if `s[i:j+1]` is a palindrome
- Iteration order: **i from n-1 to 0**, because `dp[i][j]` depends on `dp[i+1][j-1]`
- Base cases:
  - Length 1 → always palindrome
  - Length 2 → check equality
- Update max palindrome as we find longer ones

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        n = len(s)
        if n == 0: return ""
        
        dp = [[False] * n for _ in range(n)]
        res = (0, 0)  # start and end indices of the max palindrome

        for i in range(n - 1, -1, -1):
            for j in range(i, n):
                if s[i] == s[j]:
                    if (j - i <= 2) or dp[i + 1][j - 1]:
                        dp[i][j] = True
                        if j - i > res[1] - res[0]:
                            res = (i, j)
        
        return s[res[0]:res[1] + 1]
```

### Complexity Analysis

- Time: O(n^2)
- Space: O(n^2)

---

## Solution 2: Expand Around Center (Optimal for Interview)

- Every palindrome can be expanded from its center
- Two types of centers:
  - Single character center (odd-length palindrome)
  - Between two characters (even-length palindrome)

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if not s: return ""
        
        res = ""

        def expand(left: int, right: int) -> str:
            while left >= 0 and right < len(s) and s[left] == s[right]:
                left -= 1
                right += 1
            return s[left + 1:right]

        for i in range(len(s)):
            # Odd-length palindrome
            p1 = expand(i, i)
            # Even-length palindrome
            p2 = expand(i, i + 1)
            res = max(res, p1, p2, key=len)
        
        return res
```

### Complexity Analysis

- Time: O(n^2) — check all centers
- Space: O(1) — no extra DP table

---

## Interview Talking Points

- DP solution: useful to show understanding of memoization
- Expand-around-center: simpler, faster in practice, constant space
- Highlight difference between odd/even length palindromes
- Explain why iteration order matters in DP

---

## Key Takeaways

- Two main approaches:
  - DP: O(n^2) time & space
  - Expand-around-center: O(n^2) time, O(1) space
- Center expansion is generally preferred in interviews
- Base cases: single characters and pairs are palindromes

