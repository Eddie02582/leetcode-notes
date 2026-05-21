# LeetCode 105 – Construct Binary Tree from Preorder and Inorder Traversal

## Problem Summary
Given two integer arrays `preorder` and `inorder` where `preorder` is the preorder traversal of a binary tree and `inorder` is the inorder traversal of the same tree, construct and return the binary tree.

Assume that **duplicate values do not exist** in the tree.

---

## Core Difficulty
1. **Root Identification:** `preorder` allows us to easily find the root, but it doesn't tell us where the left and right subtrees end.
2. **Boundary Splitting:** `inorder` allows us to separate the left and right subtrees, but we need to find the root's index first.
3. **Index Mapping Performance:** Finding the root inside `inorder` using a linear scan (`O(n)`) at every recursion step slows down the overall algorithm significantly.

This makes **efficiently mapping indices between preorder and inorder arrays to achieve linear time complexity** the central concern.

---

## Approach Overview
There are two main approaches to solve this:
1. **Naive Array Slicing**
2. **HashMap-optimized Pointers (Preferred)**

Both follow the same divide-and-conquer logic, but they differ drastically in memory efficiency and time complexity.

---

## Approach 1: Naive Array Slicing

### Idea
- Use `preorder[0]` to create the root node.
- Find the root's index in `inorder` using `.index()`.
- Slice the `preorder` and `inorder` arrays into left and right sub-arrays, then pass them recursively.

### Characteristics
- Very easy to write and intuitive during an initial brainstorm.
- **Time Complexity:** `O(n^2)` because finding the index takes `O(n)` and slicing arrays takes `O(n)` at each node.
- **Space Complexity:** `O(n^2)` due to copying elements to create new slices at each recursion depth.

### Key Limitation
Array slicing copies data under the hood. In an interview, creating new arrays at every recursive step is considered a massive red flag for performance.

---

## Approach 2: HashMap-optimized Pointers (Preferred)

### Idea
- Create a **HashMap (`inorder_map`)** before doing any recursion. This stores `{value: index}` pairs for the `inorder` array, reducing root lookup time from `O(n)` to `O(1)`.
- Use an elegant **global pointer (`pre_idx`)** to track our current position in the `preorder` traversal.
- Instead of cutting arrays, pass boundary indices `(in_left, in_right)` to define the current subtree in `inorder`.

### Key Mechanics
1. **Preorder Pointer Control:** Every time we build a node, we consume one element from `preorder` (`pre_idx += 1`). Because preorder visits nodes in **Root -> Left -> Right** order, the global pointer naturally picks up the correct root for the next sub-tree.
2. **Boundary Definition:** The `inorder` boundaries control when recursion stops. If `in_left > in_right`, it means the current subtree is empty (returns `None`).

---

## Full Python Code (LeetCode Format)

```python
from typing import List, Optional

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        # Build the hashmap to find inorder indices in O(1)
        inorder_map = {val: idx for idx, val in enumerate(inorder)}
        
        # Global pointer to track the current root in preorder
        self.pre_idx = 0
        
        def helper(in_left: int, in_right: int) -> Optional[TreeNode]:
            # Base case: if there are no elements to construct the subtree
            if in_left > in_right:
                return None
            
            # Select the current root value from preorder and increment pointer
            root_val = preorder[self.pre_idx]
            root = TreeNode(root_val)
            self.pre_idx += 1
            
            # Find where this root splits the inorder array
            root_idx = inorder_map[root_val]
            
            # Build left and right subtrees
            # MUST build Left first because preorder sequence is Root -> Left -> Right
            root.left = helper(in_left, root_idx - 1)
            root.right = helper(root_idx + 1, in_right)
            
            return root
        
        return helper(0, len(inorder) - 1)
```

---

## Complexity Analysis
- **Time Complexity:** `O(n)`
  Building the HashMap takes `O(n)`. The `helper` function is called exactly n times (once for each node), and inside the function, all operations (HashMap lookup, pointer increments) take `O(1)`.
- **Space Complexity:** `O(n)`
  The HashMap stores n elements, consuming `O(n)` space. The recursion stack takes `O(h)` space where h is the tree height (worst case `O(n)` for a skewed tree, best case `O(log n)` for a balanced tree).

---

## Comparison Summary

| Aspect | Naive Slicing | HashMap + Pointers |
|---|---|---|
| Time Complexity | `O(n^2)` | `O(n)` |
| Space Complexity | `O(n^2)` | `O(n)` |
| Modifies/Copies Arrays | Yes (Creates copies) | No (Uses index boundaries) |
| Root Lookup in Inorder | `O(n)` linear scan | `O(1)` map lookup |
| Interview preference | ❌ | ✅ |

---

## Interview Talking Points & Deep Dives

### 1. Why must we build the Left subtree before the Right subtree?
- **Interviewer:** "Look at your helper function. Can I swap the order and build `root.right` before `root.left`?"
- **Your Answer:** "No, the order cannot be swapped in this implementation. This is because we are iterating through the `preorder` array using a single forward pointer (`self.pre_idx`). The preorder traversal sequence is strictly **Root -> Left -> Right**. This means in the `preorder` array, the elements representing the entire left subtree appear immediately after the root, followed by the right subtree elements. If you try to build the right subtree first, `self.pre_idx` will incorrectly read the left subtree's data as the right subtree's root, completely corrupting the tree structure."

### 2. What happens if the tree contains duplicate values?
- **Interviewer:** "The problem description says 'duplicate values do not exist'. What breaks if there ARE duplicates?"
- **Your Answer:** "The algorithm will fail because **the unique mapping breaks**. If duplicates exist, our HashMap `inorder_map = {val: idx}` will only store the last occurrence of that value. When we look up `root_idx = inorder_map[root_val]`, we might get the wrong boundary index, which will partition the left and right subtrees incorrectly. To fix this, we would have to abandon the HashMap and do a linear search within the current `(in_left, in_right)` boundary to find the correct index, which degrades the time complexity back to `O(n^2)` in the worst case."

### 3. Can you optimize the Space Complexity to O(1) auxiliary space? (Follow-up for Senior)
- **Interviewer:** "Your HashMap solution takes `O(n)` space. Can you optimize the auxiliary space to `O(1)` (excluding the returned tree)?"
- **Your Answer:** "Yes, we can achieve `O(1)` auxiliary space (ignoring the call stack) by using an **Iterative Approach with a Stack**. By maintaining a stack of nodes and a pointer for the `inorder` array, we can determine when a left subtree has finished expanding. When the top of the stack matches the current element in `inorder`, it means we have reached the leftmost node and must pop to move to the right subtree."

#### Iterative Python Code Solution (For Interview Display):
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        if not preorder:
            return None
        
        root = TreeNode(preorder[0])
        stack = [root]
        in_idx = 0
        
        for i in range(1, len(preorder)):
            pre_val = preorder[i]
            node = stack[-1]
            
            # If the top of the stack doesn't equal the current inorder element,
            # it means we are still moving down the left branch.
            if node.val != inorder[in_idx]:
                node.left = TreeNode(pre_val)
                stack.append(node.left)
            else:
                # If it matches, we have hit the bottom-left. 
                # Pop out all nodes that belong to the finished left subtrees.
                while stack and stack[-1].val == inorder[in_idx]:
                    node = stack.pop()
                    in_idx += 1
                # The last popped node is the closest ancestor, 
                # and the new preorder value must be its right child.
                node.right = TreeNode(pre_val)
                stack.append(node.right)
                
        return root
```

---

## Key Takeaway
> Array slicing forces `O(n^2)` copies and scans.
> Pre-indexing `inorder` with a HashMap reduces lookups to `O(1)`.
> By passing integer boundaries instead of slicing, we transform an expensive exponential-looking problem into a clean, linear `O(n)` operation.

---

## One-sentence Interview Answer
> I use a HashMap to cache the indices of the `inorder` array for `O(1)` root lookup, and use a global pointer to traverse `preorder` sequentially while passing index boundaries to avoid expensive array slicing, achieving an optimal `O(n)` time and space complexity.