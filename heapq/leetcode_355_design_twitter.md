# LeetCode 355 – Design Twitter

## Problem Summary

Design a simplified Twitter with the following operations:
- `postTweet(userId, tweetId)` → post a tweet
- `getNewsFeed(userId)` → get the 10 most recent tweet IDs from the user and the people they follow
- `follow(followerId, followeeId)` → follow someone
- `unfollow(followerId, followeeId)` → unfollow someone

> Core difficulty: efficiently merge the most recent tweets from multiple users, ensuring only the 10 newest are returned.

---

## Key Insight

- **News feed retrieval** is essentially **k-way merge** problem.
- Each followee (and self) has a list of tweets sorted by time.
- We only need **latest 10 tweets** from each user.
- Using a **max-heap** lets us efficiently always select the most recent tweet across all followees.

---

## Implementation (Heap / K-way Merge)

```python
from collections import defaultdict
import heapq
from typing import List

class Twitter:

    def __init__(self):
        self.timer = 0
        self.tweets = defaultdict(list)  # userId -> list of (timestamp, tweetId)
        self.follows = defaultdict(set)  # userId -> set of followees

    def postTweet(self, userId: int, tweetId: int) -> None:
        self.timer += 1
        self.tweets[userId].append((self.timer, tweetId))

    def getNewsFeed(self, userId: int) -> List[int]:
        heap = []
        users = self.follows[userId] | {userId}  # include self

        # Initialize heap with the latest tweet from each user
        for u in users:
            if self.tweets[u]:
                t, tid = self.tweets[u][-1]
                idx = len(self.tweets[u]) - 1
                heap.append((-t, tid, u, idx))  # max-heap via negative timestamp

        heapq.heapify(heap)
        res = []

        while heap and len(res) < 10:
            _, tid, u, idx = heapq.heappop(heap)
            res.append(tid)

            # Push the next most recent tweet from the same user
            if idx > 0:
                nt, ntid = self.tweets[u][idx-1]
                heapq.heappush(heap, (-nt, ntid, u, idx-1))

        return res

    def follow(self, followerId: int, followeeId: int) -> None:
        if followerId != followeeId:
            self.follows[followerId].add(followeeId)

    def unfollow(self, followerId: int, followeeId: int) -> None:
        self.follows[followerId].discard(followeeId)
```

---

## Key Points

1. **Timestamp-based ordering**: each tweet has a global timestamp  
2. **K-way merge using heap**: each user's tweets are sorted → merge latest 10 efficiently  
3. Only maintain **indices of latest tweets**, no need to merge entire lists  
4. `follow` / `unfollow` → straightforward set operations  
5. Heap ensures that retrieving the news feed is **O(n log k)** with k = number of followees + 1  

---

## Complexity Analysis

- **Time**:  
  - `postTweet`: O(1)  
  - `follow` / `unfollow`: O(1)  
  - `getNewsFeed`: O(k log k + 10 log k) ≈ O(k log k), k = #followees + 1  
- **Space**: O(T + F), T = total tweets, F = total follow relations  

---

## Interview Talking Points

- **Heap-based k-way merge** is key for `getNewsFeed`  
- Can discuss **alternative approaches** (e.g., storing pre-merged feed) and tradeoffs  
- Emphasize **lazy evaluation**: only fetch as needed for top 10  
- Show understanding of **time vs space optimization**  

---

## Key Takeaways

- Treat `getNewsFeed` as a **classic k-way merge problem**  
- Heap enables always selecting the most recent tweet efficiently  
- Tweet storage per user + max-heap → scalable design  
- This is a **standard pattern for "merge sorted streams" problems**

