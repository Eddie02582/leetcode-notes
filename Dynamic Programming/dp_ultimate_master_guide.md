
# 🚀 DP Ultimate Master Guide（Interview Full Version）

This document summarizes major Dynamic Programming patterns for coding interviews.

---

# 1️⃣ 0/1 Knapsack

## Pattern
Choose or not choose each item (use once).

## Template
```python
for num in nums:
    for s in range(capacity, num - 1, -1):
        dp[s] = max(dp[s], dp[s - num] + value)
```

## Variants
- 416 Partition Equal Subset Sum
- 474 Ones and Zeroes
- 494 Target Sum (after transform)

---

# 2️⃣ Complete Knapsack

## Pattern
Unlimited use of items.

## Template
```python
for num in nums:
    for s in range(num, target + 1):
        dp[s] += dp[s - num]
```

## Variants
- 322 Coin Change
- 518 Coin Change II

---

# 3️⃣ Counting DP

## Pattern
Count number of ways instead of max value.

## Template
```python
dp[0] = 1
for num in nums:
    for s in range(target, num - 1, -1):
        dp[s] += dp[s - num]
```

---

# 4️⃣ 2D Knapsack

Two capacity constraints.

## Template
```python
for item in items:
    for i in range(m, w1 - 1, -1):
        for j in range(n, w2 - 1, -1):
            dp[i][j] = max(dp[i][j],
                           dp[i - w1][j - w2] + 1)
```

---

# 5️⃣ LCS (Longest Common Subsequence)

## State
dp[i][j] = LCS length of text1[:i] and text2[:j]

## Template
```python
for i in range(1, m + 1):
    for j in range(1, n + 1):
        if text1[i-1] == text2[j-1]:
            dp[i][j] = dp[i-1][j-1] + 1
        else:
            dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

---

# 6️⃣ LIS (Longest Increasing Subsequence)

## O(n^2) DP
```python
for i in range(n):
    for j in range(i):
        if nums[j] < nums[i]:
            dp[i] = max(dp[i], dp[j] + 1)
```

## O(n log n) Greedy + Binary Search

---

# 7️⃣ Interval DP

## Pattern
Split interval into subproblems.

## Example Template
```python
for length in range(2, n + 1):
    for i in range(n - length + 1):
        j = i + length - 1
        for k in range(i, j):
            dp[i][j] = min(dp[i][j],
                           dp[i][k] + dp[k+1][j] + cost)
```

---

# 8️⃣ State Machine DP (Stock Problems)

States:
- hold
- not_hold
- cooldown

General idea:
```python
hold = -prices[0]
not_hold = 0

for price in prices[1:]:
    hold = max(hold, not_hold - price)
    not_hold = max(not_hold, hold + price)
```

Variants:
- 121 Stock I
- 122 Stock II
- 309 Cooldown
- 714 Fee
- 123 / 188 K transactions

---

# 9️⃣ Digit DP

Used when:
- Count numbers within range
- Digit constraints

State usually:
(position, tight, sum, other_states)

---

# 🔥 Interview Recognition Guide

If problem says:
- “Choose or not choose” → 0/1 Knapsack
- “Unlimited usage” → Complete Knapsack
- “Count ways” → Counting DP
- “Two constraints” → 2D Knapsack
- “Substring / subsequence” → LCS type
- “Interval merging” → Interval DP
- “Buy/sell states” → State Machine DP
- “Digits in range” → Digit DP

---

# 🏆 Final Advice

Dynamic Programming problems differ mainly in:

1. State definition
2. Transition relation
3. Loop order
4. Dimension reduction possibility

Master state definition first.
Transition naturally follows.

If you understand patterns instead of memorizing solutions,
you can solve almost all DP interview problems.
