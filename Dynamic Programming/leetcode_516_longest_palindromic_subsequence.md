# LeetCode 516 --- Longest Palindromic Subsequence

------------------------------------------------------------------------

## 🧠 Problem Summary

Given a string `s`, return the length of the **longest palindromic
subsequence**.

-   Subsequence ≠ substring (can skip characters)
-   Order must be preserved

------------------------------------------------------------------------

# 🔥 Core Insight

👉 Interval DP

We consider substring s\[i:j\]

------------------------------------------------------------------------

# 🧩 DP State

dp\[i\]\[j\] = LPS length in s\[i:j+1\]

------------------------------------------------------------------------

# ⚡ Transition

If s\[i\] == s\[j\]:

dp\[i\]\[j\] = dp\[i+1\]\[j-1\] + 2

Else:

dp\[i\]\[j\] = max(dp\[i+1\]\[j\], dp\[i\]\[j-1\])

------------------------------------------------------------------------

# 🧱 Base Case

dp\[i\]\[i\] = 1

------------------------------------------------------------------------

# 🔄 Iteration Order

i from n-1 → 0\
j from i → n-1

------------------------------------------------------------------------

# ✅ Solution 1: 2D DP

``` python
class Solution:
    def longestPalindromeSubseq(self, s: str) -> int:
        n = len(s)
        dp = [[0] * n for _ in range(n)]

        for i in range(n - 1, -1, -1):
            for j in range(i, n):
                if i == j:
                    dp[i][j] = 1
                elif s[i] == s[j]:
                    dp[i][j] = dp[i+1][j-1] + 2
                else:
                    dp[i][j] = max(dp[i+1][j], dp[i][j-1])

        return dp[0][n-1]
```

------------------------------------------------------------------------

# 🚀 Solution 2: 1D DP

``` python
class Solution:
    def longestPalindromeSubseq(self, s: str) -> int:
        n = len(s)
        dp = [0] * n

        for i in range(n - 1, -1, -1):
            dp[i] = 1
            prev = 0

            for j in range(i + 1, n):
                temp = dp[j]

                if s[i] == s[j]:
                    dp[j] = prev + 2
                else:
                    dp[j] = max(dp[j], dp[j - 1])

                prev = temp

        return dp[n - 1]
```

------------------------------------------------------------------------

# 📊 Complexity

Time: O(n²)\
Space: O(n²) → O(n)

------------------------------------------------------------------------

# 🧠 Pattern

Interval DP\
String DP

------------------------------------------------------------------------

# 🏁 Key Takeaways

1.  Palindrome → two pointers\
2.  Subsequence → DP\
3.  Match → shrink both\
4.  Not match → try skipping

------------------------------------------------------------------------

# 🎯 Interview Questions

Q1. Substring vs subsequence?\
Q2. Why dp\[i+1\]\[j-1\]?\
Q3. Can use LCS? → Yes: LCS(s, reverse(s))\
Q4. Why reverse iteration?

------------------------------------------------------------------------

END
