# LeetCode 354 --- Russian Doll Envelopes

------------------------------------------------------------------------

## 🧠 Problem Summary

Given envelopes:

    envelopes[i] = [width, height]

One envelope can fit into another if:

    w1 < w2 AND h1 < h2

Return the maximum number of envelopes.

------------------------------------------------------------------------

# 🔥 Core Insight

This problem =

Sort + Longest Increasing Subsequence (LIS)

But this is **2D LIS**.

------------------------------------------------------------------------

# 🚀 Sorting Trick (Very Important)

Sort by:

1.  width increasing
2.  height decreasing (if same width)

``` python
envelopes.sort(key=lambda x: (x[0], -x[1]))
```

Why?

Same width cannot nest.

Descending prevents fake LIS.

------------------------------------------------------------------------

# After Sorting

We only need LIS on height.

Example:

    [2,3]
    [5,4]
    [6,7]
    [6,4]

Heights:

    3,4,7,4

LIS = 3

------------------------------------------------------------------------

# ✅ Optimal Solution (O n log n)

``` python
from bisect import bisect_left

class Solution:
    def maxEnvelopes(self, envelopes):
        envelopes.sort(key=lambda x: (x[0], -x[1]))
        
        lis = []
        
        for _, h in envelopes:
            pos = bisect_left(lis, h)
            
            if pos == len(lis):
                lis.append(h)
            else:
                lis[pos] = h
                
        return len(lis)
```

------------------------------------------------------------------------

# DP Solution (O n\^2)

``` python
class Solution:
    def maxEnvelopes(self, envelopes):
        envelopes.sort(key=lambda x: (x[0], -x[1]))
        n = len(envelopes)
        
        dp = [1] * n
        
        for i in range(n):
            for j in range(i):
                if envelopes[j][1] < envelopes[i][1]:
                    dp[i] = max(dp[i], dp[j] + 1)
        
        return max(dp)
```

------------------------------------------------------------------------

# 📊 Complexity

  Method     Time         Space
  ---------- ------------ -------
  LIS + BS   O(n log n)   O(n)
  DP         O(n²)        O(n)

------------------------------------------------------------------------

# 🧠 Pattern Classification

2D LIS\
Sort + LIS\
Patience Sorting

------------------------------------------------------------------------

# 🏁 Key Takeaways

1.  2D increasing → sort first dimension
2.  same width → height DESC
3.  reduce to 1D LIS
4.  binary search LIS

------------------------------------------------------------------------

# 🎯 Interview Questions

### Why height descending?

Prevent same width being counted.

### Why LIS works?

After sorting, width already valid.

Only need increasing height.

### Can we use DP?

Yes but slower O(n²)

### Similar Problems

300 LIS\
646 Pair Chain\
1713 LIS variant

------------------------------------------------------------------------

END
