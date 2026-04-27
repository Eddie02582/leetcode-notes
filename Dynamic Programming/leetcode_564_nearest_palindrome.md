# LeetCode 564 --- Find the Closest Palindrome

------------------------------------------------------------------------

## 🧠 Problem Summary

Given a string `n` representing an integer, return the **closest
palindrome** (not including itself).

If tie → return **smaller one**.

------------------------------------------------------------------------

# 🔥 Core Insight

只需要考慮 5 個候選回文：

1.  prefix - 1
2.  prefix
3.  prefix + 1
4.  999...9
5.  100...001

------------------------------------------------------------------------

# Solution

``` python
class Solution:
    def nearestPalindromic(self, n: str) -> str:
        length = len(n)
        num = int(n)

        candidates = set()

        candidates.add(10**(length-1) - 1)
        candidates.add(10**length + 1)

        prefix = int(n[:(length+1)//2])

        for diff in (-1,0,1):
            p = str(prefix + diff)

            if length % 2 == 0:
                pal = p + p[::-1]
            else:
                pal = p + p[:-1][::-1]

            candidates.add(int(pal))

        candidates.discard(num)

        return str(min(candidates, key=lambda x:(abs(x-num), x)))
```

------------------------------------------------------------------------

# Complexity

Time: O(1)\
Space: O(1)

------------------------------------------------------------------------

# Interview Questions

Q1 為什麼只要 5 個候選?\
Q2 為什麼要 999..?\
Q3 為什麼要 100..001?\
Q4 odd / even 差別?

------------------------------------------------------------------------
