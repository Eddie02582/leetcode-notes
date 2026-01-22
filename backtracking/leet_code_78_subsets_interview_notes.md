# LeetCode 78 – Subsets

## Problem Summary
Given a list of distinct integers `nums`, return all possible subsets (the power set).  
- Each element may appear **at most once**.  
- The solution set **must not contain duplicate subsets**.

---

## Core Difficulty
- Generating all 2^n subsets  
- Understanding DFS / backtracking pattern  
- Optionally, handling duplicates in variants (Subsets II)

---

## Three Common Approaches

### 1️⃣ Iterative (build 0~n element subsets)
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        output = [[]]
        for num in nums:
            temp = []
            for curr in output:
                temp.append(curr + [num])
            output += temp
        return output
```
**Pros:** Simple, non-recursive; easy to implement quickly  

**Cons:** Less intuitive for DFS/backtracking interview questions; harder to extend to variants  
**Thought process:** Build subsets by expanding existing ones; provides alternative perspective to backtracking  

---

### 2️⃣ Backtracking by fixed subset length k
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def backtrack(first=0, curr=[]):
            if len(curr) == k:
                output.append(curr[:])
                return
            for i in range(first, n):
                curr.append(nums[i])
                backtrack(i + 1, curr)
                curr.pop()
        
        output = []
        n = len(nums)
        for k in range(n + 1):
            backtrack()
        return output
```
**Pros:** Demonstrates combination C(n,k) backtracking; control subset length  

**Cons:** Extra loop over k; slightly more complex than standard DFS  

---

### 3️⃣ Backtracking: each element choose or not choose ✅ **Recommended**
```python
from typing import List

class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        ans = []
        n = len(nums)
        
        def backtracking(index: int, path: List[int]):
            if index == n:
                ans.append(path[:])
                return
            # Not choose current element
            backtracking(index + 1, path)
            # Choose current element
            backtracking(index + 1, path + [nums[index]])
        
        backtracking(0, [])
        return ans
```
**Pros:** Clean DFS/backtracking pattern; easy to explain with decision tree; easy to extend to Subsets II or sum constraints  

**Cons:** Uses recursion (depth = n)  

---

## Comparison of the Three Methods
- All three methods have **same time complexity**: O(n * 2^n)  
  - n = length of subset (for copying)  
  - 2^n = total number of subsets  
- Key difference is **thinking style / extendibility**:  
  - Iterative → build from previous subsets, intuitive and fast  
  - Fixed k → combination-style DFS, good for controlled length  
  - Each element choose/not choose → standard decision tree, preferred for interviews  

---

## Recommended Approach for Interviews
- Use **each element select/not-select DFS/backtracking**  

- Can draw a small **decision tree** for 2–3 elements  

- Easily generalizable for related problems  

---

## Complexity Analysis
- Time: O(2^n) — must generate all subsets  

- Space: O(n) recursion stack + O(2^n) output  

---

## Possible Interview Questions
| Question | Hint / How to Answer |
|----------|--------------------|
| Why choose backtracking over iterative? | Shows decision tree; can extend to constraints/duplicates |
| How do you generate subsets of a fixed length k? | Loop over k and use combination-style backtracking |
| How would you handle duplicates (like Subsets II)? | Sort the array, skip duplicates at same recursion depth |
| Can you do it without recursion? | Yes, iterative subset building (approach 1) |
| Complexity analysis? | Time O(2^n), Space O(n + 2^n) |
| How to explain on whiteboard? | Draw decision tree: each node = select/not-select element |

---

## Key Takeaways
- Backtracking (each element select/not-select) is most versatile for interviews  

- Iterative methods are faster to code but less generalizable  

- Sorting + skip duplicates needed for variants with repeated numbers  

- All three methods have the same asymptotic time complexity (O(n * 2^n))  
- Always explain your choice and trade-offs clearly

