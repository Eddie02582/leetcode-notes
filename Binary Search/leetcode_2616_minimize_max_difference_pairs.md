# LeetCode 2616 --- Minimize the Maximum Difference of Pairs

------------------------------------------------------------------------

## 🧠 Problem Summary

Given:

-   An integer array `nums`
-   An integer `p`

You must form **exactly p pairs** such that:

-   Each element is used at most once
-   Minimize the **maximum difference** among all pairs

Return the minimized maximum difference.

------------------------------------------------------------------------

# 🔥 Core Insight

👉 Binary Search on Answer + Greedy

------------------------------------------------------------------------

# 🚀 Key Observation

Fix max_diff:

Can we form ≥ p pairs where diff ≤ max_diff?

If YES → try smaller\
If NO → need larger

------------------------------------------------------------------------

# ⚡ Greedy Strategy

After sorting:

Always pair adjacent elements.

    if nums[i+1] - nums[i] <= max_diff:
        count += 1
        i += 2
    else:
        i += 1

------------------------------------------------------------------------

# 🧩 Algorithm

1.  Sort nums\
2.  Binary search answer\
3.  Greedy check feasibility

------------------------------------------------------------------------

# ✅ Code

``` python
class Solution:
    def minimizeMax(self, nums, p):
        if p == 0:
            return 0

        nums.sort()
        n = len(nums)

        def can_form_p_pairs(max_diff):
            count = 0
            i = 0
            while i < n - 1:
                if nums[i+1] - nums[i] <= max_diff:
                    count += 1
                    i += 2
                else:
                    i += 1
                if count >= p:
                    return True
            return False

        l, r = 0, nums[-1] - nums[0]

        while l < r:
            mid = (l + r) // 2
            if can_form_p_pairs(mid):
                r = mid
            else:
                l = mid + 1

        return l
```

------------------------------------------------------------------------

# 📊 Complexity

  Type    Complexity
  ------- ----------------------
  Time    O(n log n + n log D)
  Space   O(1)

------------------------------------------------------------------------

# 🧠 Pattern Classification

Binary Search on Answer\
Greedy Pairing

------------------------------------------------------------------------

# 🎯 Interview Possible Questions

### Q1. Why binary search works?

Monotonic property: If diff works → bigger diff also works

------------------------------------------------------------------------

### Q2. Why greedy works?

Using smallest differences maximizes number of pairs.

------------------------------------------------------------------------

### Q3. Why adjacent pairing?

Sorted array → adjacent gives minimal diff.

------------------------------------------------------------------------

### Q4. Edge case p = 0?

Return 0

------------------------------------------------------------------------

END
