# LeetCode 40 – Combination Sum II

## Problem Summary
Given a list of candidate numbers (which may contain duplicates) and a target number, find all unique combinations where the candidate numbers sum to the target. Each number may be used **at most once**.

The key challenge is handling **duplicate values** efficiently while using backtracking.

---

## Core Difficulty
- Candidates can contain duplicates
- Each element can only be used once
- The result must not contain duplicate combinations

This makes **duplicate handling during backtracking** the central concern.

---

## Approach Overview
There are two common approaches:

1. **Set-based deduplication**
2. **Sorted backtracking with duplicate skipping (preferred)**

Both are correct, but they differ significantly in efficiency and design quality.

---

## Approach 1: Set-based Deduplication

### Idea
- Use backtracking to generate combinations
- Prune when the current sum exceeds the target
- Store each valid combination in a `set` to remove duplicates

### Characteristics
- Can prune by stopping recursion when `current_sum > target`
- Deduplicates **final results only**
- Does **not** prevent exploring duplicate branches in the DFS tree
- Higher memory usage

### Key Limitation
Using a set removes duplicate answers **after** DFS finishes, but it does not reduce the number of DFS paths explored.

---

## Approach 2: Sorted Backtracking (Preferred)

### Idea
- Sort the candidates array first
- Use backtracking
- Skip duplicate values **at the same recursion depth**
- Stop early when the current value exceeds the remaining target

### Key Pruning Techniques

1. **Duplicate-branch pruning (width pruning)**
```python
if i > start and candidates[i] == candidates[i - 1]:
    continue
```

2. **Early termination (depth pruning)**
```python
if candidates[i] > remaining:
    break
```

These prevent generating duplicate combinations in the first place.

---

## Full Python Code (LeetCode Format)
```python
from typing import List

class Solution:
    def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
        candidates.sort()
        res = []
        
        def backtrack(start: int, path: List[int], remain: int):
            if remain == 0:
                res.append(path[:])
                return
            for i in range(start, len(candidates)):
                # Duplicate-branch pruning
                if i > start and candidates[i] == candidates[i - 1]:
                    continue
                # Early termination
                if candidates[i] > remain:
                    break
                backtrack(i + 1, path + [candidates[i]], remain - candidates[i])
        
        backtrack(0, [], target)
        return res
```

---

## Complexity Analysis
- Sorting: `O(n log n)`
- Backtracking (worst case): `O(2^n)`

Since backtracking is exponential, the sorting cost is relatively small and is dominated by DFS.
Sorting significantly reduces the actual search space by pruning duplicate branches early.

---

## Comparison Summary
| Aspect | Set-based | Sorted Backtracking |
|------|----------|--------------------|
| Removes duplicate results | Yes | Yes |
| Prevents duplicate DFS paths | No | Yes |
| Prunes when sum exceeds target | Yes | Yes |
| Memory usage | Higher | Lower |
| Interview preference | ❌ | ✅ |

---

## Interview Talking Points
- Using a set deduplicates results, not search paths
- Sorting enables skipping duplicates at the same recursion depth
- Sorting allows earlier pruning and a smaller DFS tree
- The `O(n log n)` sort cost is negligible compared to `O(2^n)` backtracking

---

## Key Takeaway
> Using a set removes duplicate results **after** DFS.
> Sorting allows us to avoid generating duplicate combinations **during** DFS.
> Because backtracking is exponential, the small sorting cost is well worth the pruning benefits.

---

## One-sentence Interview Answer
> I sort the input so I can skip duplicates at the same recursion depth and prune duplicate branches early; the sort cost is negligible compared to exponential backtracking.

