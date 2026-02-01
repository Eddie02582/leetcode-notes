# 📈 Best Time to Buy and Sell Stock – 全系列狀態 DP 面試指南

## 概念總結：狀態 DP (State DP)

- **狀態**：今天手上持有股票或沒有股票，以及額外限制（冷卻、手續費、交易次數）
- **轉移方程**：今天某個狀態的最大收益 = max(選擇不同動作後的收益)
- **核心思路**：每個題目的不同限制只會改變狀態和轉移公式

### 常見狀態

| 狀態 | 含義 |
|------|------|
| hold | 今天手上持有股票 |
| not_hold / cash | 今天手上沒有股票且可買 |
| cooldown | 今天手上沒有股票，但因昨天賣股票所以不能買 |
| k_trans | 今天已完成交易次數，對應限制交易次數 |

---

## Problem 121 – Best Time to Buy and Sell Stock (一次交易)

### 狀態定義
- hold: 無需追蹤，直接記錄 min_price
- not_hold: 最大收益

### 核心公式
```python
res = max(res, price - min_price)
min_price = min(min_price, price)
```
- 每天更新手上最低價，計算賣出收益

### 時間與空間
- Time: O(n)
- Space: O(1)

### 面試 Talking Points
- 單次交易，贪心即可  
- 不需建立完整 DP table

---

## Problem 122 – Best Time to Buy and Sell Stock II (無限交易)

### 狀態定義
- hold: 手上持有股票
- not_hold: 手上沒有股票

### 轉移公式
```python
not_hold = max(not_hold, hold + price)
hold = max(hold, not_hold - price)
```
- 或者用貪心：累加所有上坡收益

### 時空複雜度
- Time: O(n)
- Space: O(1)

### 面試講解重點
- Greedy vs DP 的對比  
- hold / not_hold 狀態轉移

---

## Problem 309 – Best Time to Buy and Sell Stock with Cooldown

### 狀態定義
- hold: 今天手上持有股票
- not_hold: 今天手上沒有股票且可買
- cooldown: 今天手上沒有股票但不能買（昨天賣股票）

### 轉移公式
```python
hold = max(prev_hold, not_hold - price)
not_hold = max(prev_not_hold, cooldown)
cooldown = prev_hold + price
```
- 每天更新三種狀態

### 面試講解
- 冷卻增加額外狀態  
- 對應轉移公式必須依賴前一天的 hold / not_hold / cooldown

---

## Problem 714 – Best Time to Buy and Sell Stock with Transaction Fee

### 狀態定義
- hold: 今天手上持有股票  
- not_hold: 今天手上沒有股票

### 轉移公式
```python
not_hold = max(prev_not_hold, hold + price - fee)
hold = max(hold, prev_not_hold - price)
```
- 賣出時減去 fee

### 面試講解
- fee 影響賣出動作  
- DP 仍然維持兩個狀態即可

---

## Problem 123 – Best Time to Buy and Sell Stock III (最多兩次交易)

### 狀態定義
- t1_cost, t1_profit: 第一次交易成本及收益  
- t2_cost, t2_profit: 第二次交易成本及收益

### 核心公式
```python
t1_cost = min(t1_cost, price)
t1_profit = max(t1_profit, price - t1_cost)
t2_cost = min(t2_cost, price - t1_profit)
t2_profit = max(t2_profit, price - t2_cost)
```
- 手動追蹤每次交易的收益

### 面試講解
- 兩次交易時，每次交易都影響後面交易成本  
- 時間: O(n), 空間: O(1)

---

## Problem 188 – Best Time to Buy and Sell Stock IV (最多 k 次交易)

### 狀態定義
- dp[i][j]: 第 i 次交易完成後，在第 j 天的最大收益  
- max_diff: 優化計算 dp[i-1][j] - prices[j]

### 核心公式
```python
for i in range(1, k+1):
    max_diff = -prices[0]
    for j in range(1, len(prices)):
        dp[i][j] = max(dp[i][j-1], prices[j] + max_diff)
        max_diff = max(max_diff, dp[i-1][j] - prices[j])
```
- 時間: O(k*n), 空間: O(k*n)
- k 大時可簡化為無限交易 O(n)

### 面試講解
- 交易次數限制 → 增加 DP 維度  
- 對比 123 和 122 的差異

---

## 總結：狀態 DP 面試心得

- 股票題目核心都是 **狀態 DP**  
- 定義狀態：手上有無股票、交易次數、冷卻或手續費  
- 設計轉移公式：最大化每個狀態今天的收益  
- 優化：空間 O(1) 或使用滾動數組  
- 面試必問：狀態定義、轉移公式、特殊限制（冷卻/手續費/交易次數）

