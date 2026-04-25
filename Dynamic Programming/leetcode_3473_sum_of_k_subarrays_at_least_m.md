# LeetCode 3473 --- Sum of K Subarrays With Length at Least M

------------------------------------------------------------------------

## 🧠 Problem Summary

You are given:

-   An integer array `nums`
-   An integer `k`
-   An integer `m`

You must select **exactly `k` non-overlapping subarrays**,\
each subarray must have **length ≥ m**,\
and maximize the **total sum**.

------------------------------------------------------------------------

## 🔥 Core Difficulty

Constraints:

-   Subarrays cannot overlap
-   Each subarray must have length ≥ m
-   Must pick exactly k segments
-   Need maximum total sum

This is a:

> Interval DP + Prefix Sum optimization problem

------------------------------------------------------------------------

# 🚀 Step 1 --- Prefix Sum

To compute subarray sums in O(1):

presum\[i\] = nums\[0\] + ... + nums\[i-1\]\
sum(i, j) = presum\[j+1\] - presum\[i\]

------------------------------------------------------------------------

# 🧩 Step 2 --- DP State Design

Let:

dp\[i\]\[j\] = maximum sum using first i elements, selecting j valid
subarrays

Answer:

dp\[n\]\[k\]

------------------------------------------------------------------------

# ⚡ Step 3 --- Transition

Two choices at position i:

1️⃣ Skip i

dp\[i\]\[j\] = dp\[i-1\]\[j\]

2️⃣ End a subarray at i

If last subarray length ≥ m:

Assume it starts at t:

length = i - t + 1 ≥ m

Then:

dp\[i\]\[j\] = max( dp\[i\]\[j\], dp\[t-1\]\[j-1\] + sum(t, i) )

------------------------------------------------------------------------

# 🧠 Naive Complexity

O(n²k)

Too slow.

------------------------------------------------------------------------

# 🚀 Step 4 --- Optimization Trick

Rewrite:

dp\[t-1\]\[j-1\] + presum\[i\] - presum\[t-1\] = presum\[i\] +
(dp\[t-1\]\[j-1\] - presum\[t-1\])

So for fixed i and j, we want:

max(dp\[t-1\]\[j-1\] - presum\[t-1\])

Maintain running maximum while iterating i.

This reduces complexity to:

O(nk)

------------------------------------------------------------------------

# ✅ Final Optimized Code (O(nk))

``` python
class Solution:
    def maxSum(self, nums: List[int], k: int, m: int) -> int:
        n = len(nums)
        presum = [0] * (n + 1)
        for i in range(n):
            presum[i+1] = presum[i] + nums[i]

        dp = [[-float('inf')] * (k + 1) for _ in range(n + 1)]
        dp[0][0] = 0

        for j in range(1, k + 1):
            best = -float('inf')
            for i in range(1, n + 1):
                dp[i][j] = dp[i-1][j]

                if i - m >= 0:
                    best = max(best, dp[i-m][j-1] - presum[i-m])

                if best != -float('inf'):
                    dp[i][j] = max(dp[i][j], presum[i] + best)

        return dp[n][k]
```

------------------------------------------------------------------------

# 📊 Complexity

Time: O(nk)\
Space: O(nk)

------------------------------------------------------------------------

# 🧠 Pattern Classification

Belongs to:

Interval DP\
Fixed number of segments\
Minimum length constraint\
Optimized using prefix sum + rolling maximum

------------------------------------------------------------------------

# 🏁 Key Takeaways

1.  Exactly K non-overlapping intervals → think dp\[i\]\[j\]
2.  dp\[t\] + prefix\[i\] - prefix\[t\] → can optimize with running max

------------------------------------------------------------------------

End of Guide.
