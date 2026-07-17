# LeetCode 153 – Find Minimum in Rotated Sorted Array

## Problem Summary

Given a sorted array of unique integers `nums` that has been rotated between `1` and `n` times (where `n` is the length of the array), find the minimum element in this array. 

For example, a sorted array `[0,1,2,4,5,6,7]` rotated 4 times becomes `[4,5,6,7,0,1,2]`.
The algorithm must run in $O(\log n)$ time complexity.

### Inputs
- `nums`: A list of unique integers of size `n` ($1 \le n \le 5000$), which is sorted in ascending order and rotated between `1` and `n` times.

### Expected Outputs
- The minimum element in the rotated sorted array.

### Key Constraints/Bounds
- $-5000 \le nums[i] \le 5000$
- All the integers in `nums` are **unique**.
- Must achieve $O(\log n)$ time complexity.

---

## Core Difficulty

The main difficulty lies in achieving a sub-linear ($O(\log n)$) search time when the array is not fully sorted in ascending order. 
- A standard binary search relies on a fully sorted array. However, a rotated sorted array is split into two sorted subarrays (e.g., `[4,5,6,7]` and `[0,1,2]`).
- We need to correctly identify which half of the array contains the minimum element (the "inflection point" or the pivot) during each binary search step.
- Common pitfalls include off-by-one errors in pointer updates (`left`, `mid`, `right`) and handling boundary conditions (like when the array is not actually rotated, i.e., already fully sorted).

---

## Approach Overview

### Approach 1: Linear Scan (Naive/Brute Force)

- **Idea:** Traverse the entire array from left to right and track the minimum element found.
- **Algorithm:**
  1. Initialize `min_val` with `nums[0]`.
  2. Loop through each element in `nums` from index `1` to `n-1`.
  3. Update `min_val` if any element is smaller than `min_val`.
  4. Return `min_val`.
- **Python Code:**
```python
class Solution:
    def findMin(self, nums: list[int]) -> int:
        min_val = nums[0]
        for num in nums:
            if num < min_val:
                min_val = num
        return min_val
```
- **Complexity:**
  - **Time Complexity:** $O(n)$ because we must visit every element in the array of size $n$ in the worst case.
  - **Space Complexity:** $O(1)$ as we only store a single variable `min_val`.
- **Pros / Cons:**
  - **Pros:** Simple to implement, works even if there are duplicate values.
  - **Cons:** Extremely inefficient for large inputs, and violates the $O(\log n)$ time complexity constraint.

### Approach 2: Binary Search (Optimal)

- **Idea:** Leverage the sorted properties of the two subarrays. By comparing the middle element `nums[mid]` with the rightmost element `nums[right]`, we can confidently discard half of the search space.
- **Algorithm:**
  1. Initialize two pointers: `left = 0` and `right = len(nums) - 1`.
  2. While `left < right`:
     - Compute the middle index: `mid = left + (right - left) // 2`.
     - Compare `nums[mid]` with `nums[right]`:
       - If `nums[mid] > nums[right]`: This implies that the rotation inflection point (and thus the minimum element) lies strictly to the right of `mid`. Thus, set `left = mid + 1`.
       - If `nums[mid] <= nums[right]`: This implies that the minimum element is either at `mid` or lies to the left of `mid`. Thus, set `right = mid`.
  3. When `left == right`, the search space has converged to a single element. Return `nums[left]` (or `nums[right]`).
- **Python Code:**
```python
class Solution:
    def findMin(self, nums: list[int]) -> int:
        left, right = 0, len(nums) - 1
        
        while left < right:
            mid = left + (right - left) // 2
            
            # If mid element is greater than right element,
            # the minimum element is in the right half.
            if nums[mid] > nums[right]:
                left = mid + 1
            # Otherwise, the minimum element is in the left half (including mid).
            else:
                right = mid
                
        return nums[left]
```
- **Complexity:**
  - **Time Complexity:** $O(\log n)$ because the search space is halved in each step of the binary search.
  - **Space Complexity:** $O(1)$ since only a few pointers are used.
- **Pros / Cons:**
  - **Pros:** Highly efficient, meets the strict $O(\log n)$ constraint, and naturally handles unrotated arrays (already sorted).
  - **Cons:** Requires precise pointer updates to prevent infinite loops.

---

## Compare Approaches

- **Time Complexity:** Approach 2 ($O(\log n)$) is exponentially faster than Approach 1 ($O(n)$).
- **Space Complexity:** Both approaches are highly memory efficient, operating in $O(1)$ auxiliary space.
- **Thinking Process Difference:** 
  - Approach 1 focuses purely on linear search without utilizing any pre-existing order or structure.
  - Approach 2 takes advantage of the "sorted" characteristics of the rotated partitions to eliminate half of the options in logarithmic steps.
- **Applicable Scenarios:** 
  - Approach 1 is only viable if the array is unsorted and has no specific structure.
  - Approach 2 is the standard, production-ready solution for searching in rotated sorted contexts.
- **Trade-offs:** 
  - Approach 2 requires slightly more analytical design to handle boundary conditions correctly, whereas Approach 1 is trivially correct but slow.

---

## Interview Recommendation

In an interview, you should acknowledge the naive $O(n)$ solution briefly to show you understand the problem, but quickly point out that the sorted nature of the array suggests an $O(\log n)$ optimization is possible.
- Pivot to **Binary Search** and explain the logic of comparing `nums[mid]` with `nums[right]` (instead of `nums[left]`, since an unrotated array would complicate the `nums[left]` comparison).
- Walk through a concrete trace with a small example, such as `[4, 5, 1, 2, 3]`.

---

## Interview Talking Points / Questions

- **Why choose `nums[right]` for comparison instead of `nums[left]`?**
  - *Answer:* Comparing `nums[mid]` with `nums[left]` is ambiguous. For example, in both `[3, 4, 5, 1, 2]` and `[1, 2, 3, 4, 5]`, `nums[mid] > nums[left]` holds true (5 > 3, and 3 > 1), but the minimum is on different sides. Comparing with `nums[right]` consistently resolves whether the right subarray is sorted or not.
- **How to handle an already sorted, unrotated array?**
  - *Answer:* The binary search logic handles this naturally. If the array is already sorted (e.g., `[1, 2, 3, 4, 5]`), `nums[mid]` (3) will be less than `nums[right]` (5), leading to `right = mid`. This continues halving the right bound until `left` and `right` converge on index `0`, returning `1`.
- **What happens if duplicate values are allowed?**
  - *Answer:* If duplicates are present (as in LeetCode 154), when `nums[mid] == nums[right]`, we cannot tell which side to search. In that case, we must decrement the right bound (`right -= 1`), causing the worst-case time complexity to degrade to $O(n)$.

---

## Key Takeaway

- **Core Algorithmic Pattern:** Binary Search on a modified search space.
- **Key Techniques:** Partition elimination by comparing `mid` against the boundaries.
- **Relation to Other Problems:**
  - LeetCode 33 - Search in Rotated Sorted Array
  - LeetCode 81 - Search in Rotated Sorted Array II (with duplicates)
  - LeetCode 154 - Find Minimum in Rotated Sorted Array II (with duplicates)
