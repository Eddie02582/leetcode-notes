# LeetCode 698 --- Partition to K Equal Sum Subsets

------------------------------------------------------------------------

## 🧠 Problem Summary

Given an integer array `nums` and an integer `k`, return **true** if it
is possible to divide the array into `k` non-empty subsets whose sums
are all equal.

------------------------------------------------------------------------

# 🔥 Core Insight

This題 =

-   Backtracking + pruning
-   Bitmask DP
-   Subset Partition

```{=html}
<!-- -->
```
    total = sum(nums)
    target = total / k

把數字分成 k 個 bucket，每個 bucket = target

------------------------------------------------------------------------

# Solution 1 --- DFS + Memo + Bitmask (Top‑Down)

``` python
class Solution:
    def canPartitionKSubsets(self, nums, k):
        total = sum(nums)
        if total % k != 0:
            return False
        
        target = total // k
        nums.sort(reverse=True)

        if nums[0] > target:
            return False

        n = len(nums)
        memo = {}

        def dfs(mask, current_sum, k_left):
            if k_left == 1:
                return True

            if current_sum == target:
                return dfs(mask, 0, k_left - 1)

            if (mask, current_sum) in memo:
                return memo[(mask, current_sum)]

            for i in range(n):
                if mask & (1 << i):
                    continue

                if i > 0 and nums[i] == nums[i-1] and not (mask & (1 << (i-1))):
                    continue

                if current_sum + nums[i] > target:
                    continue

                if dfs(mask | (1<<i), current_sum + nums[i], k_left):
                    memo[(mask, current_sum)] = True
                    return True

            memo[(mask, current_sum)] = False
            return False

        return dfs(0,0,k)
```

------------------------------------------------------------------------

# Pruning 技巧

### 1. sort descending

大數先放 → 更快剪枝

### 2. duplicate skip

避免對稱組合

### 3. bucket fill 完

    current_sum == target

直接下一桶

### 4. k_left == 1

最後一桶必然成立

------------------------------------------------------------------------

# Solution 2 --- Bitmask DP (Bottom‑Up)

``` python
class Solution:
    def canPartitionKSubsets(self, nums, k):
        total = sum(nums)
        if total % k != 0:
            return False
        
        target = total // k
        nums.sort(reverse=True)

        if nums[0] > target:
            return False

        n = len(nums)
        size = 1 << n

        dp = [-1] * size
        dp[0] = 0

        for mask in range(size):
            if dp[mask] == -1:
                continue

            for i in range(n):
                if mask & (1 << i):
                    continue

                nxt = dp[mask] + nums[i]

                if nxt > target:
                    continue

                new_mask = mask | (1<<i)
                dp[new_mask] = nxt % target

                if nxt == target:
                    break

        return dp[size-1] == 0
```

------------------------------------------------------------------------

# DP State

    dp[mask] = current bucket sum

用 % target

自動換桶

------------------------------------------------------------------------

# Complexity

DFS + memo\
O(k \* 2\^n)

Bitmask DP\
O(n \* 2\^n)

n \<= 16

------------------------------------------------------------------------

# Pattern Classification

Bitmask DP\
Subset partition\
Backtracking + pruning\
State compression

------------------------------------------------------------------------

# Key Takeaways

1.  total % k == 0
2.  target = total/k
3.  bitmask 表示使用狀態
4.  fill bucket
5.  sort descending
6.  duplicate skip

------------------------------------------------------------------------

# Interview Questions

### Q1 為什麼 k_left == 1 return true

最後一桶一定成立

### Q2 為什麼要 sort reverse

更快剪枝

### Q3 為什麼 dp\[mask\] % target

自動換桶

### Q4 時間複雜度

O(n \* 2\^n)

### Q5 Similar

416 Partition Equal Subset Sum\
473 Matchsticks to Square\
1723 Minimum Time to Finish Jobs

------------------------------------------------------------------------
