# LeetCode 63 -- Unique Paths II (Unique Paths with Obstacles)

## Problem Summary

Given an m x n grid filled with 0s and 1s:

-   0 → empty cell
-   1 → obstacle

Return the number of unique paths from top-left to bottom-right. You can
only move right or down.

------------------------------------------------------------------------

# Core Concept: Grid DP

This is a classic **2D Dynamic Programming** problem.

## State Definition

dp\[i\]\[j\] = number of unique paths to reach cell (i, j)

Transition:

If obstacleGrid\[i\]\[j\] == 0:

    dp[i][j] = dp[i-1][j] + dp[i][j-1]

If obstacleGrid\[i\]\[j\] == 1:

    dp[i][j] = 0

------------------------------------------------------------------------

# Solution 1: 2D DP (Standard)

``` python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        if obstacleGrid[0][0] == 1 or obstacleGrid[-1][-1] == 1:
            return 0

        m, n = len(obstacleGrid), len(obstacleGrid[0])
        dp = [[0] * n for _ in range(m)]

        # initialize first row
        for j in range(n):
            if obstacleGrid[0][j] == 1:
                break
            dp[0][j] = 1

        # initialize first column
        for i in range(m):
            if obstacleGrid[i][0] == 1:
                break
            dp[i][0] = 1

        for i in range(1, m):
            for j in range(1, n):
                if obstacleGrid[i][j] == 0:
                    dp[i][j] = dp[i-1][j] + dp[i][j-1]

        return dp[m-1][n-1]
```

Time Complexity: O(mn)\
Space Complexity: O(mn)

------------------------------------------------------------------------

# Solution 2: 1D DP (Space Optimized)

Observation:

Each cell only depends on: - top (previous row, same column) - left
(current row, previous column)

So we can compress to 1D DP.

``` python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        m, n = len(obstacleGrid), len(obstacleGrid[0])

        if obstacleGrid[0][0] == 1:
            return 0

        dp = [0] * n
        dp[0] = 1

        for i in range(m):
            for j in range(n):
                if obstacleGrid[i][j] == 1:
                    dp[j] = 0
                elif j > 0:
                    dp[j] += dp[j-1]

        return dp[-1]
```

Time Complexity: O(mn)\
Space Complexity: O(n)

------------------------------------------------------------------------

# Solution 3: DFS + Memo (Top-Down)

``` python
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        from functools import lru_cache
        m, n = len(obstacleGrid), len(obstacleGrid[0])

        @lru_cache(None)
        def dfs(i, j):
            if i >= m or j >= n or obstacleGrid[i][j] == 1:
                return 0
            if i == m - 1 and j == n - 1:
                return 1
            return dfs(i+1, j) + dfs(i, j+1)

        return dfs(0, 0)
```

Time Complexity: O(mn)\
Space Complexity: O(mn) (recursion + memo)

------------------------------------------------------------------------

# Interview Talking Points

1.  State Definition: dp\[i\]\[j\] represents number of paths to reach
    that cell.
2.  Initialization: first row/column stops when obstacle appears.
3.  Transition: dp\[i\]\[j\] = top + left.
4.  Space Optimization: 2D → 1D because only previous row needed.
5.  Follow-up:
    -   What if diagonal movement allowed?
    -   What if obstacles dynamic?
    -   What if need to output path?

------------------------------------------------------------------------

# Comparison

  Approach     Time    Space   Notes
  ------------ ------- ------- -------------------------------
  2D DP        O(mn)   O(mn)   Most intuitive
  1D DP        O(mn)   O(n)    Optimal space
  DFS + Memo   O(mn)   O(mn)   Good for recursion discussion

------------------------------------------------------------------------

# Key Takeaways

-   Classic grid DP problem
-   Obstacle only changes dp to 0
-   Space can be optimized to 1D
-   Important to correctly initialize first row and column
