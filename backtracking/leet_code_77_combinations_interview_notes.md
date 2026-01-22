# LeetCode 77 – Combinations

## Problem Summary
Given two integers `n` and `k`, return all possible combinations of `k` numbers chosen from the range `[1, n]`.

---

## Core Difficulty
- Generating combinations (not permutations)
- Controlling recursion depth
- Applying correct and safe pruning

---

## Backtracking (with Pruning) — Interview Version

> **Note**: This version keeps the original algorithm and pruning logic intact.  
> Only variable and function names are optimized for readability and interview clarity.

```python
class Solution:
    def combine(self, n: int, k: int) -> List[List[int]]:
        results = []

        def backtrack(start: int, combination: List[int]):
            if len(combination) == k:
                results.append(combination[:])
                return

            for num in range(start, n + 1):
                # Pruning: remaining numbers are not enough
                if len(combination) + (n - num + 1) < k:
                    return

                combination.append(num)
                backtrack(num + 1, combination)
                combination.pop()

        backtrack(1, [])
        return results
```

---

## Pruning Logic Explained

```python
if len(combination) + (n - num + 1) < k:
    return
```

- `len(combination)` → number of elements already chosen
- `(n - num + 1)` → maximum number of elements still available
- If the maximum possible size is still smaller than `k`, this branch cannot succeed

### Why `return` instead of `continue`
- `num` increases monotonically
- If the current `num` is insufficient, all following choices will also fail
- Safe to terminate the loop early

---

## Complexity Analysis

- Time: **O(C(n, k))**
- Space:
  - O(k) recursion depth
  - O(C(n, k)) for output storage

Pruning significantly reduces the search space in practice.

---

## Interview Talking Points

- Use backtracking to build combinations in increasing order
- Prune branches when remaining numbers are insufficient
- Avoid unnecessary recursion

---

## Key Takeaways
- This is the standard and optimal backtracking solution for combinations
- Pruning is mathematically safe and improves performance
- Clear naming improves readability without changing logic

