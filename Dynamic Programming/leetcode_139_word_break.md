# 🔤 LeetCode 139 – Word Break

## Problem Summary

Given a string `s` and a dictionary of strings `wordDict`, determine if `s` can be segmented into a space-separated sequence of one or more dictionary words.

Constraints:
- Words can be reused multiple times
- Order matters
- Dictionary lookup efficiency is critical

**Example:**
```text
s = "leetcode"
wordDict = ["leet","code"]
Output: true
```

---

## Core Difficulty

- Overlapping subproblems → DP / DFS + memo
- Fast substring lookup
- Handling large dictionaries efficiently
- Avoiding exponential recursion

---

## Solution Overview

There are **4 common solutions** based on:
- DP vs DFS + Memo
- Using **Set** vs **Trie**

| Approach | Lookup Structure | Time (avg) | Space | Interview Value |
|--------|----------------|-----------|-------|-----------------|
| DP | Set | ⭐⭐⭐⭐ | Low | ⭐⭐⭐⭐⭐ |
| DP | Trie | ⭐⭐⭐⭐ | Medium | ⭐⭐⭐⭐ |
| DFS + Memo | Set | ⭐⭐⭐⭐ | Low | ⭐⭐⭐⭐⭐ |
| DFS + Memo | Trie | ⭐⭐⭐⭐ | Medium | ⭐⭐⭐⭐ |

---

## Solution 1: DP + Set (Most Common)

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        word_set = set(wordDict)
        n = len(s)
        dp = [False] * (n + 1)
        dp[0] = True

        for i in range(1, n + 1):
            for j in range(i):
                if dp[j] and s[j:i] in word_set:
                    dp[i] = True
                    break
        return dp[n]
```

### Complexity

- Time: O(n²)
- Space: O(n)

---

## Solution 2: DP + Trie

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for c in word:
            node = node.children.setdefault(c, TrieNode())
        node.is_end = True


class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        trie = Trie()
        for w in wordDict:
            trie.insert(w)

        n = len(s)
        dp = [False] * (n + 1)
        dp[0] = True

        for i in range(n):
            if not dp[i]:
                continue
            node = trie.root
            for j in range(i, n):
                if s[j] not in node.children:
                    break
                node = node.children[s[j]]
                if node.is_end:
                    dp[j + 1] = True
        return dp[n]
```

---

## Solution 3: DFS + Memo + Set

```python
from functools import lru_cache

class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        word_set = set(wordDict)

        @lru_cache(None)
        def dfs(start):
            if start == len(s):
                return True
            for end in range(start + 1, len(s) + 1):
                if s[start:end] in word_set and dfs(end):
                    return True
            return False

        return dfs(0)
```

---

## Solution 4: DFS + Memo + Trie

```python
class Solution:
    def wordBreak(self, s: str, wordDict: List[str]) -> bool:
        trie = Trie()
        for w in wordDict:
            trie.insert(w)

        from functools import lru_cache

        @lru_cache(None)
        def dfs(i):
            node = trie.root
            for j in range(i, len(s)):
                if s[j] not in node.children:
                    return False
                node = node.children[s[j]]
                if node.is_end and dfs(j + 1):
                    return True
            return i == len(s)

        return dfs(0)
```

---

## Set vs Trie – Interview Comparison

| Aspect | Set | Trie |
|-----|-----|------|
| Lookup | O(1) average | O(k) per char |
| Memory | Low | Higher |
| Prefix pruning | ❌ | ✅ |
| Large dictionary | OK | Better |
| Implementation | Simple | Complex |
| Interview clarity | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

---

## Interview Talking Points

- Why DP works: overlapping subproblems
- DFS without memo is exponential
- Set is usually sufficient unless prefix pruning is required
- Trie shines when dictionary is huge or prefix-heavy
- DP vs DFS: bottom-up vs top-down tradeoff

---

## Key Takeaways

- Word Break has **4 classic solutions**
- DP + Set is usually the best interview answer
- Trie is an optimization, not always required
- Memoization is critical to avoid TLE
