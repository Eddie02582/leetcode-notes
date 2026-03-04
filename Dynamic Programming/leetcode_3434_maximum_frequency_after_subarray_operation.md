# LeetCode 3434 --- Maximum Frequency After Subarray Operation

------------------------------------------------------------------------

## 🧠 Problem Summary

Given:

-   Integer array `nums`
-   Integer `k`

You may choose **at most one subarray** and add an integer `x` to every
element in that subarray.

Goal:

Maximize the frequency of value `k` after the operation.

------------------------------------------------------------------------

# 🔥 Core Insight

If we want to convert value `v` into `k`, we must choose:

x = k - v

This is the only possible x that converts `v → k`.

Inside the chosen subarray:

-   Each `v` becomes `k`
-   Each existing `k` becomes something else
-   Other values shift but do not matter

Net gain:

(# of v in subarray) − (# of k in subarray)

------------------------------------------------------------------------

# 🚀 Reformulation

Let:

total_k = total occurrences of k in nums

For each distinct value `v ≠ k`, define:

gain\[i\] = +1 if nums\[i\] == v -1 if nums\[i\] == k 0 otherwise

Then:

max_gain = maximum subarray sum of gain

Answer:

total_k + max_gain

------------------------------------------------------------------------

# 🧠 Why Enumerate Each v?

Each v requires unique x = k - v.

Only one operation allowed → one x only.

So treat each v independently.

------------------------------------------------------------------------

# ⚡ Algorithm

For each v ≠ k:

1.  Run Kadane on gain array
2.  Track best gain
3.  Return maximum

------------------------------------------------------------------------

# ✅ Code

``` python
class Solution:
    def maxFrequency(self, nums, k):
        total_k = nums.count(k)
        ans = total_k

        values = set(nums)
        values.discard(k)

        for v in values:
            gain = 0
            best = 0

            for num in nums:
                if num == v:
                    gain += 1
                elif num == k:
                    gain -= 1

                gain = max(gain, 0)
                best = max(best, gain)

            ans = max(ans, total_k + best)

        return ans
```

------------------------------------------------------------------------

# 📊 Complexity

  Type    Complexity
  ------- ------------
  Time    O(n · u)
  Space   O(1)

u = number of distinct values

------------------------------------------------------------------------

# 🧠 Pattern Classification

Subarray gain modeling\
Kadane transformation\
One-operation interval optimization

------------------------------------------------------------------------

# 🎯 Interview Possible Questions

### Q1. Why not convert multiple values?

Different v need different x.

------------------------------------------------------------------------

### Q2. Why Kadane?

Gain array models exactly (#v − #k).

------------------------------------------------------------------------

### Q3. If two operations allowed?

Becomes selecting two disjoint maximum subarrays → DP variant.

------------------------------------------------------------------------

END
