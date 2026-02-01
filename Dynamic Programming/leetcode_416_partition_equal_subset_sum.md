# 🔢 LeetCode 416 – Partition Equal Subset Sum

## Problem Summary

Given a list of positive integers `nums`, determine if it can be partitioned into two subsets with equal sum.

Constraints:
- Each element is positive
- Return True / False

**Example:**
```text
nums = [1,5,11,5]
Output: True  (subsets: [1,5,5] and [11])
```

---

## Core Difficulty

- Subset sum problem → classic DP
- Bottom-up vs Top-down memoization
- Optimization for large sums / number of elements
- Handling impossible cases early

---

## Solution 1: Bottom-Up DP (Iterative)

```python
class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        total = sum(nums)
        if total % 2 != 0:
            return False
        target = total // 2
        n = len(nums)

        dp = [False] * (target + 1)
        dp[0] = True

        for num in nums:
            for t in range(target, num - 1, -1):
                dp[t] = dp[t] or dp[t - num]
        return dp[target]
```

### Complexity

- Time: O(n * target)
- Space: O(target) (can also use 2D array)
- Bottom-up guarantees iterative solution without recursion stack

---

## Solution 2: Top-Down DFS + Memo (Recursive)

```python
from functools import lru_cache

class Solution:
    def canPartition(self, nums: List[int]) -> bool:
        total = sum(nums)
        if total % 2 != 0:
            return False
        target = total // 2
        n = len(nums)

        @lru_cache(None)
        def dfs(i, curr_sum):
            if curr_sum == target:
                return True
            if curr_sum > target or i == n:
                return False
            # choose nums[i] or skip
            return dfs(i + 1, curr_sum + nums[i]) or dfs(i + 1, curr_sum)

        return dfs(0, 0)
```

### Complexity

- Time: O(n * target) with memoization
- Space: O(n * target) memo + recursion stack
- Top-down is intuitive for interviews; memo avoids recomputation

---

## Interview Talking Points

- Bottom-up uses iterative DP array; top-down uses recursion + memo
- Early pruning: if sum is odd → return False immediately
- Space optimization: bottom-up 2D → 1D rolling array
- Relation to 0-1 knapsack problem
- Can discuss extension to counting subsets or minimizing difference

---

## Key Takeaways

- Recognize **subset sum** as DP problem
- Both **Bottom-Up and Top-Down** are valid
- Memoization is essential to avoid exponential recursion
- Bottom-up is memory-efficient; Top-down is easier to explain

