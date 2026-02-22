
# 🔥 Knapsack DP Master Guide（Interview Edition）

This document summarizes all major knapsack DP patterns frequently tested in interviews.

---

# 1️⃣ 0/1 Knapsack（Max Value）

## 特徵
- 每個物品只能使用一次
- 求最大值 / 是否可行

## 狀態
dp[s] = 容量為 s 時的最大價值

## 模板
```python
for num in nums:
    for s in range(capacity, num - 1, -1):  # backward
        dp[s] = max(dp[s], dp[s - num] + value)
```

## 經典題
- 416 Partition Equal Subset Sum
- 474 Ones and Zeroes
- 494 Target Sum（轉換後）

---

# 2️⃣ 0/1 Knapsack（Count Ways）

## 特徵
- 問有幾種方法
- 每個物品只能用一次

## 狀態
dp[s] = 組成 s 的方法數

## 模板
```python
dp[0] = 1
for num in nums:
    for s in range(target, num - 1, -1):
        dp[s] += dp[s - num]
```

---

# 3️⃣ Complete Knapsack（Unlimited Use）

## 特徵
- 每個物品可使用無限次

## 模板
```python
dp[0] = 1
for num in nums:
    for s in range(num, target + 1):  # forward
        dp[s] += dp[s - num]
```

## 經典題
- 322 Coin Change
- 518 Coin Change II

---

# 4️⃣ 2D Knapsack

## 特徵
- 兩個容量限制

## 狀態
dp[i][j] = 使用 i 容量1、j 容量2 的最大值

## 模板
```python
for item in items:
    for i in range(m, weight1 - 1, -1):
        for j in range(n, weight2 - 1, -1):
            dp[i][j] = max(dp[i][j],
                           dp[i - weight1][j - weight2] + 1)
```

## 經典題
- 474 Ones and Zeroes

---

# 🧠 Loop 方向規則

| 類型 | 方向 |
|------|------|
| 0/1 Knapsack | 倒序 |
| Complete Knapsack | 正序 |

倒序 → 防止重複使用  
正序 → 允許重複使用  

---

# 🎯 Final Takeaway

背包問題的差異只在：

- 是否可重複使用
- 求最大值還是計數
- 有幾個容量維度
- 是否可壓縮維度


