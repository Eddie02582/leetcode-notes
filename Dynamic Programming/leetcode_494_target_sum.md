
# LeetCode 494 – Target Sum

## Problem Summary

Given an integer array `nums` and an integer `target`,  
Return the number of ways to assign `+` and `-` signs to make the sum equal to `target`.

Example:
nums = [1,1,1,1,1], target = 3  
Output = 5

---

# Core Insight

Each number has two choices:
+num or -num

This is a:
- Counting DP problem
- State DP problem
- 0/1 Knapsack transformation problem
- Subset Sum variation

---

# Approach 1 – 2D HashMap DP

## State Definition

dp[i][s] = number of ways to reach sum s using first i numbers

## Transition

For each number:
- Add it → s + num
- Subtract it → s - num

## Code

```python
from collections import defaultdict

class Solution:
    def findTargetSumWays(self, nums, target):
        n = len(nums)
        dp = [defaultdict(int) for _ in range(n + 1)]
        dp[0][0] = 1

        for i in range(1, n + 1):
            num = nums[i - 1]
            for s, count in dp[i - 1].items():
                dp[i][s + num] += count
                dp[i][s - num] += count

        return dp[n][target]
```

Time Complexity: O(n * states)  
Space Complexity: O(n * states)

---

# Approach 2 – 1D Rolling HashMap

Since dp[i] only depends on dp[i-1], we can compress space.

## Code

```python
from collections import defaultdict

class Solution:
    def findTargetSumWays(self, nums, target):
        dp = defaultdict(int)
        dp[0] = 1

        for num in nums:
            next_dp = defaultdict(int)
            for s, count in dp.items():
                next_dp[s + num] += count
                next_dp[s - num] += count
            dp = next_dp

        return dp[target]
```

Time Complexity: O(n * states)  
Space Complexity: O(states)

---

# Approach 3 – Transform to 0/1 Knapsack

## Mathematical Transformation

Let:
P = sum of positive numbers  
N = sum of negative numbers  
S = total sum of nums  

We know:

P - N = target  
P + N = S  

Solving:

P = (target + S) / 2

So the problem becomes:

Count subsets whose sum equals P.

---

## Important Conditions

- If abs(target) > total → return 0  
- If (total + target) is odd → return 0  

---

## 0/1 Knapsack Code

```python
class Solution:
    def findTargetSumWays(self, nums, target):
        total = sum(nums)

        if abs(target) > total or (total + target) % 2:
            return 0

        P = (total + target) // 2

        dp = [0] * (P + 1)
        dp[0] = 1

        for num in nums:
            for s in range(P, num - 1, -1):
                dp[s] += dp[s - num]

        return dp[P]
```

Time Complexity: O(n * P)  
Space Complexity: O(P)

---

# Why Iterate Backward in Knapsack?

Because this is a 0/1 knapsack problem.

Backward iteration ensures:
Each number is used at most once.

If iterated forward, it becomes complete knapsack (unlimited usage).

---

# Interview Questions You Might Be Asked

1. Why can Target Sum be converted to Subset Sum?
2. Why must we check (total + target) % 2?
3. Why iterate backward in 0/1 knapsack?
4. What if nums contains zeros?
5. What is the difference between count knapsack and max knapsack?
6. Time complexity comparison between hashmap DP and knapsack DP?

---

# Key Takeaways

- This is counting DP.
- Can be solved with state DP using HashMap.
- Best optimization is transforming to subset sum.
- Classic interview problem connecting Target Sum and 0/1 Knapsack.
