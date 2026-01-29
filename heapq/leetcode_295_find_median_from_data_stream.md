# LeetCode 295 – Find Median from Data Stream

## Problem Summary

Design a data structure that supports:

- `addNum(num)` → add a number into the data structure
- `findMedian()` → return the median of all inserted numbers

Median definition:
- If total count is odd → middle value
- If even → average of two middle values

---

## Core Idea

Maintain **two heaps**:

- `left`: max heap → stores the **smaller half** of numbers
- `right`: min heap → stores the **larger half** of numbers

Invariant:

```
len(left) == len(right)   or   len(left) == len(right) + 1
```

So:

- Median is either:
  - top of `left`, or
  - average of tops of `left` and `right`

---

## Why Two Heaps Work

We want:

```
max(left) <= min(right)
```

So:

- left stores smaller half
- right stores larger half
- heap tops always give median candidates

---

## Simplified Heap Strategy (Interview Preferred)

This is the **most concise and elegant** version.

```python
import heapq

class MedianFinder:

    def __init__(self):
        self.left = []   # max heap (store negatives)
        self.right = []  # min heap

    def addNum(self, num: int) -> None:
        # 1. Always push into left
        heapq.heappush(self.left, -num)

        # 2. Move the largest from left to right
        heapq.heappush(self.right, -heapq.heappop(self.left))

        # 3. Balance: ensure left >= right
        if len(self.right) > len(self.left):
            heapq.heappush(self.left, -heapq.heappop(self.right))

    def findMedian(self) -> float:
        if len(self.left) > len(self.right):
            return -self.left[0]
        else:
            return (-self.left[0] + self.right[0]) / 2
```

---

## Why This Simplification Works

This logic always guarantees:

```
max(left) <= min(right)
```

Step-by-step reasoning:

1. Push into `left`
2. Move max(left) → right, fixing ordering
3. Rebalance sizes so left ≥ right

This avoids complicated conditional logic and edge-case handling.

---

## Complexity Analysis

- **addNum:** `O(log n)`  
  Heap push + pop operations

- **findMedian:** `O(1)`

- **Space:** `O(n)`

---

## Follow-up Optimizations

1. **All numbers in the range [0, 100]**

- Use a **count array** of size 101 instead of heaps.
- Maintain total count and prefix sum to find median in O(1) to O(101) time.
- Space: O(101) → O(1) effectively.

```python
counts = [0] * 101
total = 0

def addNum(num):
    counts[num] += 1
    total += 1

def findMedian():
    cum = 0
    for i, c in enumerate(counts):
        cum += c
        if cum >= (total + 1)//2:
            median1 = i
            break
    if total % 2 == 1:
        return median1
    # find second median for even total
```

2. **99% of numbers in [0, 100]**

- Use a **hybrid approach**:
  - Count array for 0–100
  - Heap for numbers outside this range
- Most operations remain O(1) or very fast due to small heap size for outliers

This is useful for streaming scenarios with bounded frequent ranges and rare outliers.

---

## Interview Talking Points

- Median = split array into two halves
- Two heaps simulate dynamic sorted structure
- Always maintain:

```
size(left) >= size(right)
```

- Heap-based balancing avoids full sorting
- Follow-up shows awareness of **bounded data optimizations** and **hybrid data structures**

---

## Key Takeaways

- Classic **two-heap design pattern**
- Simplified 3-step insertion is clean and safe
- Count array optimization is very effective for bounded value ranges
- Hybrid approach works when most numbers are bounded but rare outliers exist

---

## Related Patterns

- Sliding window median
- Running statistics
- Streaming percentile problems

