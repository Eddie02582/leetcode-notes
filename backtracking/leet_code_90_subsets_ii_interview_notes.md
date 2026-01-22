# LeetCode 90 – Subsets II

## Problem Summary
Given an integer array `nums` that may contain duplicates, return all possible **unique subsets** (the power set).  
- The solution set must not contain duplicate subsets.

---

## Core Difficulty
- Handling **duplicate elements** correctly  
- Avoiding duplicate subsets  
- Understanding **same-level (width) pruning** vs **depth pruning**

---

## Two Main Approaches

---

## 1️⃣ Iterative Approach with Index Control
This is an extension of Subsets (78) iterative method, enhanced to handle duplicates.

```python
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        output = [[]]
        start = 0  # start index of subsets added in previous round
        
        for i in range(len(nums)):
            if i > 0 and nums[i] == nums[i - 1]:
                begin = start
            else:
                begin = 0
            
            start = len(output)
            for j in range(begin, start):
                output.append(output[j] + [nums[i]])
        
        return output
```

### Key Idea
- Sort first to group duplicates
- If current number is duplicate:
  - Only extend subsets **created in the previous iteration**
- Prevents generating duplicate subsets

### Pros
- Iterative, no recursion
- Efficient and elegant once understood

### Cons
- Logic is less intuitive
- Harder to derive on the spot during interviews
- Less flexible for other backtracking variants

---

## 2️⃣ DFS / Backtracking with Same-Level Pruning ✅ **Recommended**

```python
class Solution:
    def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        res = []
        path = []

        def dfs(start):
            res.append(path[:])
            for i in range(start, len(nums)):
                # Same-level duplicate pruning
                if i > start and nums[i] == nums[i - 1]:
                    continue
                path.append(nums[i])
                dfs(i + 1)
                path.pop()

        dfs(0)
        return res
```

### Key Idea
- Sort array first
- At each recursion level:
  - Skip duplicate values **at the same depth**
- Ensures uniqueness without using `set`

### Pruning Explanation

#### Duplicate-branch pruning (width pruning)
```python
if i > start and nums[i] == nums[i - 1]:
    continue
```
- Prevents duplicate branches at the same recursion level

---

## Comparison of Two Methods

| Aspect | Iterative | DFS / Backtracking |
|------|----------|-------------------|
| Duplicate handling | Index control | Same-level pruning |
| Readability | Medium | High |
| Extendibility | Low | High |
| Interview friendliness | Medium | High |
| Generalization | Hard | Easy |

---

## Complexity Analysis

- Sorting: O(n log n)
- Total subsets: 2^n
- Time: **O(n · 2^n)** (copying subsets)
- Space: **O(n · 2^n)** (result storage)

---

## Interview Strategy

Recommended explanation flow:

> "First, I sort the array to group duplicates. Then I use backtracking.  
> At each recursion level, I skip duplicate numbers to avoid generating the same subset."

Optional follow-up:

> "Alternatively, this can also be done iteratively by only extending subsets created in the previous step, but DFS is more flexible and easier to generalize."

---

## Possible Interview Questions

| Question | Suggested Answer |
|--------|-----------------|
| Why sorting is required? | To group duplicates for pruning |
| Why use `i > start`? | To ensure pruning only at same level |
| Difference from Subsets (78)? | Need duplicate handling |
| Can iterative approach work? | Yes, but logic is more complex |
| Why not use set? | Extra space + less control over pruning |

---

## Key Takeaways
- Sorting is essential for duplicate handling
- Same-level pruning is the core idea
- DFS/backtracking is preferred for interviews
- Iterative method works but is more problem-specific
- Avoid `set` unless explicitly asked