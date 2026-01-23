# LeetCode 46 – Permutations

## Problem Summary
Given an integer array `nums` with distinct elements, return all possible permutations.

## Core Difficulty
- Handling order of elements (permutation vs combination)
- Avoiding duplicate usage of the same element in one permutation
- Managing recursion depth

## Standard Backtracking — Interview Version
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        results = []
        path = []
        used = [False] * len(nums)

        def backtrack():
            if len(path) == len(nums):
                results.append(path[:])
                return
            for i in range(len(nums)):
                if used[i]:
                    continue
                path.append(nums[i])
                used[i] = True
                backtrack()
                path.pop()
                used[i] = False

        backtrack()
        return results

## Complexity Analysis
- Time: O(n!)
- Space: O(n) recursion + O(n!) output

## Interview Talking Points
- Use a `used` array to track elements already in the path
- DFS explores all possible orders
- Backtracking template is universal

## Key Takeaways
- Permutations are order-sensitive combinations
- Track usage to avoid duplicates
- Standard backtracking template applies
