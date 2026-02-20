# LeetCode 2767 - Minimum Beautiful Substrings

## Problem Summary

Given a binary string `s`, split it into the minimum number of
substrings such that:

-   Each substring represents a power of 5 in binary
-   No substring has leading zeros
-   Return the minimum number of substrings
-   If impossible, return -1

------------------------------------------------------------------------

# Core Insight

This is a classic **interval DP (cutting DP)** problem.

It belongs to:

-   Word Break type
-   Coin Change (minimum count)
-   Partition DP

State definition follows:

> dp\[i\] = minimum number of beautiful substrings that can form
> s\[0:i\]

This is a **prefix DP** definition.

------------------------------------------------------------------------

# Precomputation

All binary powers of 5 within length constraint:

1 101 11001 1111101 1001110001 ...

We generate them until length \<= 15 (since s.length \<= 15).

------------------------------------------------------------------------

# Bottom-Up DP (Iterative)

## State

dp\[i\] = min substrings for prefix s\[0:i\]

## Initialization

dp\[0\] = 0\
Others = inf

## Transition

For every i: - If dp\[i\] is reachable - Try every power string p - If
s\[i:i+len(p)\] == p dp\[i+len(p)\] = min(dp\[i+len(p)\], dp\[i\] + 1)

------------------------------------------------------------------------

## Code (Bottom-Up)

``` python
class Solution:
    def minimumBeautifulSubstrings(self, s: str) -> int:
        n = len(s)
        
        powers = set()
        num = 1
        while num <= 2**15:
            powers.add(bin(num)[2:])
            num *= 5

        dp = [float('inf')] * (n + 1)
        dp[0] = 0

        for i in range(n):
            if dp[i] == float('inf'):
                continue

            for p in powers:
                length = len(p)
                if i + length <= n and s[i:i+length] == p:
                    dp[i+length] = min(dp[i+length], dp[i] + 1)

        return dp[n] if dp[n] != float('inf') else -1
```

Time Complexity: O(n \* number_of_powers)

------------------------------------------------------------------------

# Top-Down DP (Memo + DFS)

## State

dfs(i) = minimum substrings for suffix s\[i:\]

## Base Case

if i == n → return 0

## Transition

Try every power string p If s\[i:i+len(p)\] == p ans = min(ans, 1 +
dfs(i+len(p)))

Memoize results.

------------------------------------------------------------------------

## Code (Top-Down)

``` python
from functools import lru_cache

class Solution:
    def minimumBeautifulSubstrings(self, s: str) -> int:
        n = len(s)
        
        powers = set()
        num = 1
        while num <= 2**15:
            powers.add(bin(num)[2:])
            num *= 5

        @lru_cache(None)
        def dfs(i):
            if i == n:
                return 0
            
            ans = float('inf')
            
            for p in powers:
                length = len(p)
                if i + length <= n and s[i:i+length] == p:
                    ans = min(ans, 1 + dfs(i + length))
            
            return ans
        
        res = dfs(0)
        return res if res != float('inf') else -1
```

Time Complexity: O(n \* number_of_powers)

------------------------------------------------------------------------

# Why This is Interval / Cutting DP

This is identical structure to:

-   Word Break
-   Coin Change (minimum coins)
-   Partition Equal Subset Sum (boolean version)

All share:

dp\[i\] = best answer for prefix ending at i

or

dfs(i) = best answer for suffix starting at i

------------------------------------------------------------------------

# Key DP Pattern Summary

Two equivalent ways to define state:

1.  **Prefix version** dp\[i\] → answer for s\[0:i\]

2.  **Suffix version** dfs(i) → answer for s\[i:\]

They are mirror images.

------------------------------------------------------------------------

# Mental Model

If problem asks:

-   "minimum cuts"
-   "minimum partitions"
-   "can split into valid parts"

Then think:

👉 Cutting DP 👉 Word Break template 👉 dp over index

------------------------------------------------------------------------

# Final Takeaway

minimumBeautifulSubstrings is:

-   Interval DP
-   Cutting DP
-   Word Break type
-   Coin Change (min version)
-   State-based DP

Same skeleton. Different condition.
