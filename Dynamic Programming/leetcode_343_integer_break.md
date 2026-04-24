# LeetCode 343 --- Integer Break

------------------------------------------------------------------------

## 🧠 Problem Summary

Given an integer `n`, break it into **at least two positive integers**
and maximize the product.

Return the maximum product.

Example:

    n = 10
    10 = 3 + 3 + 4
    3 * 3 * 4 = 36

------------------------------------------------------------------------

# 🔥 Core Insight

This problem belongs to:

-   Integer Partition
-   DP (拆分問題)
-   Math Greedy

核心：

    n = a + b + c + ...
    maximize a * b * c ...

------------------------------------------------------------------------

# Solution 1 --- DFS + Memo (Top‑Down)

``` python
class Solution:
    def __init__(self):        
        self.memo = {} 

    def integerBreak(self, n: int) -> int:
        
        def dfs(start, remaining):
            if remaining == 0:
                return 1
            
            state = (start, remaining)
            if state in self.memo:
                return self.memo[state]
            
            max_prod = 0
            
            for i in range(start, remaining + 1):
                if i == n:
                    continue
                
                res = dfs(i, remaining - i)
                
                if res > 0:
                    max_prod = max(max_prod, i * res)
            
            self.memo[state] = max_prod
            return max_prod

        return dfs(1, n)
```

------------------------------------------------------------------------

# Solution 2 --- DFS + lru_cache

``` python
from functools import lru_cache

class Solution:
    def integerBreak(self, n: int) -> int:
        
        @lru_cache(None)
        def dfs(start, remaining):
            if remaining == 0:
                return 1
            
            max_prod = 0
            
            for i in range(start, remaining + 1):
                if i == n:
                    continue
                
                res = dfs(i, remaining - i)
                max_prod = max(max_prod, i * res)
                
            return max_prod

        return dfs(1, n)
```

------------------------------------------------------------------------

# Solution 3 --- Bottom‑Up DP

    dp[i] = max product of i

Transition:

    j*(i-j)
    j*dp[i-j]

``` python
class Solution:
    def integerBreak(self, n: int) -> int:
        dp = [0] * (n + 1)
        dp[2] = 1
        
        for i in range(3, n + 1):
            for j in range(1, i // 2 + 1):
                dp[i] = max(
                    dp[i],
                    j * (i - j),
                    j * dp[i - j]
                )
        
        return dp[n]
```

------------------------------------------------------------------------

# Solution 4 --- Greedy (Math)

Use as many **3** as possible

``` python
class Solution:
    def integerBreak(self, n: int) -> int:
        if n == 2: return 1
        if n == 3: return 2
        
        res = 1
        while n > 4:
            res *= 3
            n -= 3
            
        return res * n
```

------------------------------------------------------------------------

# 📊 Complexity

  Method      Time    Space
  ----------- ------- -------
  DFS memo    O(n²)   O(n²)
  DFS cache   O(n²)   O(n²)
  DP          O(n²)   O(n)
  Greedy      O(n)    O(1)

------------------------------------------------------------------------

# 🧠 Pattern Classification

Integer Partition DP\
Unbounded split DP\
Greedy math

------------------------------------------------------------------------

# 🏁 Key Takeaways

1.  拆分問題 → DP\
2.  try j + (i-j)\
3.  greedy uses 3\
4.  special case 4 = 2\*2

------------------------------------------------------------------------

# 🎯 Interview Possible Questions

### Why greedy uses 3?

3 gives max multiplication growth

### Why special case 4?

3+1 worse than 2+2

### Why j only half?

Symmetry

### DP vs greedy?

DP general\
Greedy optimal

------------------------------------------------------------------------

END
