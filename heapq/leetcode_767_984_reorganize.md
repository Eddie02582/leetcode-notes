# LeetCode 767 & 984 – Reorganize String / String Without k Consecutive Letters

## Problem Summary

- General problem: Rearrange characters in a string `s` so that **no character repeats more than `k` times consecutively**.  
- **767 – Reorganize String** → `k=1`, multi-letter  
- **984 – String Without AAA or BBB** → `k=2`, only 2 letters  

> Observation: 984 is a special case of 767. Using `k` and heap, we can generalize both.

---

## Core Difficulty

- Avoiding **k consecutive duplicates**  
- Picking letters based on **remaining frequency**  
- Handling multiple letters vs two-letter special case  
- Edge case: impossible arrangement

---

## General Greedy + Heap Strategy (supports k consecutive constraint)

```python
from collections import Counter
import heapq
from typing import List

class Solution:
    def reorganizeStringK(self, s: str, k: int) -> str:
        count = Counter(s)
        max_heap = [(-freq, ch) for ch, freq in count.items()]
        heapq.heapify(max_heap)
        
        cold = None   # temporarily hold blocked character
        continue_cnt = 0
        res = ""
        
        while max_heap or cold:
            if not max_heap:
                return ""  # impossible
            
            freq, ch = heapq.heappop(max_heap)
            
            # check consecutive count
            if res and res[-1] == ch:
                continue_cnt += 1
            else:
                continue_cnt = 1
            
            res += ch
            freq += 1  # used one occurrence
            
            # return previously blocked character
            if cold:
                heapq.heappush(max_heap, cold)
                cold = None
            
            # block if k consecutive reached
            if freq < 0:
                if continue_cnt == k:
                    cold = (freq, ch)
                else:
                    heapq.heappush(max_heap, (freq, ch))
        
        return res
```

---

## Key Points

1. Use **max-heap** to always pick the character with most remaining occurrences  
2. Track **consecutive count** of last character  
3. If `k` consecutive reached, temporarily **block** this character (cold) for one round  
4. If heap is empty but cold exists → impossible  
5. **767:** multi-letter, k=1 → prevents adjacent duplicates  
6. **984:** two letters, k=2 → can simplify without heap

---

## Complexity Analysis

| Case | Time | Space |
|------|------|-------|
| General (multi-letter) | O(n log k) → heap operations, n = string length, k = #unique letters | O(k + n) |
| 2-letter special case | O(A + B) → linear | O(A + B) for output |

---

## Interview Talking Points

- **Generalize 767 and 984 using `k`**  
- **Greedy + heap** works for multiple letters  
- **Simplified greedy** works for only 2 letters  
- Discuss edge case handling and impossibility  
- Emphasize **consecutive count tracking** and "cold" temporary blocking

---

## Key Takeaways

- This framework unifies **Reorganize String** problems with arbitrary `k`  
- **Heap ensures always pick most frequent available character**  
- **Cold mechanism** prevents violating k consecutive constraint  
- Can be extended to other constraints on repeated characters

