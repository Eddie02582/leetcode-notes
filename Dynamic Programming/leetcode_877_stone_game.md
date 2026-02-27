# LeetCode 877 - Stone Game (Full Method Comparison)

------------------------------------------------------------------------

# Problem Summary

Alice and Bob take turns picking stones from either end of the array.
Both play optimally.

Return True if Alice can win.

Constraints: - piles.length is even - sum(piles) is odd - No tie
situation

------------------------------------------------------------------------

# Core Classification

This problem belongs to:

• Game DP\
• Minimax DP\
• Interval DP\
• Zero-Sum Game

There are two main modeling philosophies:

1️⃣ 淨值法（Score Difference Method）\
2️⃣ 總分法（Total Score Method）

------------------------------------------------------------------------

# 1️⃣ Top-Down + Memoization（淨值法）

dp\[i\]\[j\] = 當前玩家在區間 \[i, j\] 能領先對手的最大分數

``` python
from functools import cache

class Solution:
    def stoneGame(self, piles):
        @cache
        def solve(i, j):
            if i == j:
                return piles[i]
            return max(
                piles[i] - solve(i + 1, j),
                piles[j] - solve(i, j - 1)
            )
        return solve(0, len(piles) - 1) > 0
```

Time Complexity: O(n²)\
Space Complexity: O(n²)

------------------------------------------------------------------------

# 2️⃣ Top-Down + PreSum（總分法）

``` python
from functools import cache

class Solution:
    def stoneGame(self, piles):
        n = len(piles)

        presum = [0] * (n + 1)
        for i in range(n):
            presum[i+1] = presum[i] + piles[i]

        @cache
        def solve(i, j):
            if i == j:
                return piles[i]

            total = presum[j+1] - presum[i]

            return max(
                total - solve(i + 1, j),
                total - solve(i, j - 1)
            )

        first = solve(0, n - 1)
        return first > (presum[n] - first)
```

------------------------------------------------------------------------

# 3️⃣ Bottom-Up 雙狀態 DP（先手/後手）

``` python
class Solution:
    def stoneGame(self, piles):
        n = len(piles)
        dp = [[[0,0] for _ in range(n)] for _ in range(n)]

        for i in range(n):
            dp[i][i][0] = piles[i]

        for i in range(n-2,-1,-1):
            for j in range(i+1,n):
                left = [dp[i+1][j][1] + piles[i], dp[i+1][j][0]]
                right = [dp[i][j-1][1] + piles[j], dp[i][j-1][0]]
                dp[i][j] = max(left, right)

        return dp[0][n-1][0] > dp[0][n-1][1]
```

------------------------------------------------------------------------

# 4️⃣ Bottom-Up 2D DP（淨值法）

``` python
class Solution:
    def stoneGame(self, piles):
        n = len(piles)
        dp = [[0]*n for _ in range(n)]

        for i in range(n):
            dp[i][i] = piles[i]

        for length in range(2, n+1):
            for i in range(n-length+1):
                j = i + length - 1
                dp[i][j] = max(
                    piles[i] - dp[i+1][j],
                    piles[j] - dp[i][j-1]
                )

        return dp[0][n-1] > 0
```

------------------------------------------------------------------------

# 5️⃣ Bottom-Up 1D DP（空間優化）

``` python
class Solution:
    def stoneGame(self, piles):
        n = len(piles)
        dp = piles[:]

        for length in range(2, n + 1):
            for i in range(n - length + 1):
                dp[i] = max(
                    piles[i] - dp[i+1],
                    piles[i+length-1] - dp[i]
                )

        return dp[0] > 0
```

------------------------------------------------------------------------

# 方法總比較

  方法              思考方式   空間    推薦程度
  ----------------- ---------- ------- ------------
  Top-Down 淨值法   最直觀     O(n²)   ⭐⭐⭐⭐
  Top-Down 總分法   真實得分   O(n²)   ⭐⭐⭐
  雙狀態 DP         完整建模   O(n²)   ⭐⭐
  Bottom-Up 2D      標準寫法   O(n²)   ⭐⭐⭐⭐
  Bottom-Up 1D      空間優化   O(n)    ⭐⭐⭐⭐⭐

------------------------------------------------------------------------

# 面試可能會問

1.  為什麼要減 dp\[i+1\]\[j\]？\
    → 因為對手也最優。

2.  為什麼可以只用一個 dp？\
    → 用分差即可代表雙方狀態。

3.  為什麼是 Interval DP？\
    → 狀態依賴更小區間。

4.  Alice 是否一定贏？\
    → 是（偶數長度策略）。

------------------------------------------------------------------------

# 核心總結

Stone Game 本質是：

Minimax + Interval DP\
兩人對抗問題 → 用分差建模\
時間複雜度 O(n²)
