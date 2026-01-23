# LeetCode 79 --- Word Search

## Problem Summary

Given an `m x n` grid of characters `board` and a string `word`,
determine whether `word` exists in the grid.

Rules: - The word must be formed by sequentially adjacent cells (up,
down, left, right). - The same cell may not be used more than once in a
path.

------------------------------------------------------------------------

## Core Idea

This is a classic **DFS + Backtracking** problem.

For each cell: 1. Try to match `word[0]` 2. Start DFS search 3. Mark
visited cells 4. Explore 4 directions 5. Backtrack if mismatch

------------------------------------------------------------------------

## Algorithm Steps

1.  Pre-check: use `Counter` to prune impossible cases.
2.  Iterate each cell as DFS starting point.
3.  Backtracking search:
    -   If current char mismatch → return False
    -   If all characters matched → return True
    -   Try 4 directions
    -   Mark visited
    -   Restore state after recursion

------------------------------------------------------------------------

## Important Optimizations

### 1. Frequency Pruning (Global Pruning)

If the board does not contain enough characters required by `word`,
return False immediately.

``` python
from collections import Counter

board_cnt = Counter(char for row in board for char in row)
word_cnt = Counter(word)

for ch in word_cnt:
    if board_cnt[ch] < word_cnt[ch]:
        return False
```

### 2. Early Exit

Once any DFS path returns `True`, stop immediately.

------------------------------------------------------------------------

## Time Complexity

-   Worst Case: **O(M × N × 4\^L)**
    -   M × N: start points
    -   4\^L: DFS branching
-   Space: **O(M × N)** (visited array + recursion stack)

------------------------------------------------------------------------

## Clean LeetCode Solution (Optimized)

``` python
from typing import List
from collections import Counter

class Solution:
    def exist(self, board: List[List[str]], word: str) -> bool:
        m, n = len(board), len(board[0])

        # 🔹 Frequency pruning
        board_cnt = Counter(char for row in board for char in row)
        word_cnt = Counter(word)
        for ch in word_cnt:
            if board_cnt[ch] < word_cnt[ch]:
                return False

        visited = [[False] * n for _ in range(m)]
        directions = [(1,0), (-1,0), (0,1), (0,-1)]

        def dfs(r, c, idx):
            if board[r][c] != word[idx]:
                return False
            if idx == len(word) - 1:
                return True

            visited[r][c] = True

            for dr, dc in directions:
                nr, nc = r + dr, c + dc
                if 0 <= nr < m and 0 <= nc < n and not visited[nr][nc]:
                    if dfs(nr, nc, idx + 1):
                        return True

            visited[r][c] = False
            return False

        for i in range(m):
            for j in range(n):
                if dfs(i, j, 0):
                    return True

        return False
```

------------------------------------------------------------------------

## Interview Discussion Points

### Q1. Why backtracking instead of DP?

-   Each path is unique.
-   Grid paths depend on visited state → cannot reuse subproblem results
    cleanly.

### Q2. Why is pruning important?

-   Without pruning → exponential explosion.
-   Frequency pruning removes impossible cases early.

### Q3. Why marking visited is critical?

-   Prevents using the same cell multiple times.
-   Guarantees correctness.

------------------------------------------------------------------------

## Key Takeaways

-   Grid search → think DFS + backtracking
-   Always consider **pruning strategies**
-   Visited marking + restoring = backtracking core

------------------------------------------------------------------------

## Patterns Learned

This problem belongs to:

> **Grid DFS + Path Backtracking Pattern**

Similar problems: - Combination Sum - Permutations - Word Search II -
Sudoku Solver
