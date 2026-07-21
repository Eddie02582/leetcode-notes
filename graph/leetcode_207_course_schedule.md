# LeetCode 207 – Course Schedule

## Problem Summary

There are a total of `numCourses` courses you have to take, labeled from `0` to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [ai, bi]` indicates that you **must** take course `bi` first if you want to take course `ai`.

- **Input:** 
  - `numCourses`: An integer representing the total number of courses.
  - `prerequisites`: A list of lists representing directed dependency edges (`bi -> ai`).
- **Output:**
  - `bool`: `True` if you can finish all courses (i.e., the dependency graph is a Directed Acyclic Graph - DAG), and `False` otherwise.
- **Key Constraints:**
  - `1 <= numCourses <= 2000`
  - `0 <= prerequisites.length <= 5000`
  - `prerequisites[i].length == 2`
  - All prerequisite pairs are unique.

---

## Core Difficulty

The fundamental difficulty of this problem is **cycle detection in a directed graph**. 
If a cycle exists (e.g., Course A depends on Course B, and Course B depends on Course A), it is impossible to finish all courses because there is no valid starting point. 

**Key Bottlenecks & Pitfalls:**
- The graph might be disconnected (multiple separate components of courses).
- Naive recursion or DFS without state tracking (memoization) will lead to **Time Limit Exceeded (TLE)** or infinite loops due to cycles.
- Handling independent courses that do not appear in any prerequisite relation.

---

## Approach Overview

### Approach 1: BFS (Kahn's Algorithm - Indegree-based Topological Sort)

- **Idea:** 
  Build a directed graph where an edge goes from a prerequisite to its dependent course (`bi -> ai`). Keep track of the "indegree" (number of incoming dependencies) for each node. Any course with an indegree of `0` has no remaining prerequisites and can be taken immediately. We process these courses via a queue, reducing the indegree of their neighbors. If we can process all courses, there are no cycles.

- **Algorithm:**
  1. Build an adjacency list `graph` and calculate the `indegree` for each course.
  2. Initialize a queue `q` containing all courses with an `indegree` of `0`.
  3. Initialize a counter `takeCourse = 0` to track how many courses we've successfully processed.
  4. While the queue is not empty:
     - Pop a `course` from the queue, and increment `takeCourse`.
     - For each of its dependent `nextCourse` in the graph, decrement its `indegree` by 1.
     - If the `indegree` of `nextCourse` becomes `0`, append it to `q`.
  5. Return `True` if `takeCourse == numCourses`, otherwise `False`.

- **Python Code:**
```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        # Step 1: Build the graph and calculate indegrees
        graph = defaultdict(list)
        indegree = [0] * numCourses

        # Prerequisite relationship: b -> a (must take b before a)
        for a, b in prerequisites:
            graph[b].append(a)
            indegree[a] += 1

        # Step 2: Queue all courses with 0 prerequisites
        q = deque([i for i in range(numCourses) if indegree[i] == 0])

        # Step 3: Process courses topologically
        takeCourse = 0
        while q:
            course = q.popleft()
            takeCourse += 1
            
            for nextCourse in graph[course]:
                indegree[nextCourse] -= 1
                if indegree[nextCourse] == 0:
                    q.append(nextCourse)

        # Step 4: Check if we successfully took all courses
        return takeCourse == numCourses
```

- **Complexity:**
  - **Time Complexity:** O(V + E) where V = numCourses and E = prerequisites.length. We visit every node and every edge exactly once.
  - **Space Complexity:** O(V + E) to store the adjacency list `graph`, the `indegree` array of size V, and the queue of size up to V.

- **Pros / Cons:**
  - **Pros:** Extremely intuitive, directly maps to real-world scheduling, and naturally extends to outputting the actual topological order (LeetCode 210).
  - **Cons:** Requires explicit tracking of indegrees.

---

### Approach 2: DFS (Cycle Detection via Three-State Coloring)

- **Idea:**
  Use Depth-First Search (DFS) to detect cycles. To prevent infinite loops and redundant work, we use three states (colors) to track the status of each node during the DFS traversal:
  - `0` (Unvisited): The node has not been visited yet.
  - `1` (Visiting): The node is currently in the active recursion stack (if we see a "visiting" node again, a cycle is detected!).
  - `2` (Visited): The node and all its descendants have been fully processed and are cycle-free.

- **Algorithm:**
  1. Build an adjacency list `graph` where an edge goes from `bi -> ai`.
  2. Create a `state` array of size `numCourses` initialized to `0`.
  3. Define a recursive helper function `has_cycle(course)`:
     - If `state[course] == 1`, return `True` (cycle detected).
     - If `state[course] == 2`, return `False` (already verified safe).
     - Set `state[course] = 1` (mark as visiting).
     - Recursively call `has_cycle` for all adjacent courses. If any returns `True`, propagate `True`.
     - Set `state[course] = 2` (mark as fully visited).
     - Return `False`.
  4. Run `has_cycle` for every course from `0` to `numCourses - 1`. If any cycle is detected, return `False`.
  5. If no cycle is found across any path, return `True`.

- **Python Code:**
```python
from collections import defaultdict
from typing import List

class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        # Step 1: Build the graph
        graph = defaultdict(list)
        for a, b in prerequisites:
            graph[b].append(a)
        
        # 0 = Unvisited, 1 = Visiting, 2 = Visited
        state = [0] * numCourses
        
        def has_cycle(course: int) -> bool:
            if state[course] == 1:
                return True   # Found a back-edge (cycle!)
            if state[course] == 2:
                return False  # Already processed and verified cycle-free
            
            # Mark as visiting
            state[course] = 1
            
            for nextCourse in graph[course]:
                if has_cycle(nextCourse):
                    return True
            
            # Mark as visited (safe)
            state[course] = 2
            return False
        
        # Step 2: Run cycle detection for each course
        for course in range(numCourses):
            if has_cycle(course):
                return False
                
        return True
```

- **Complexity:**
  - **Time Complexity:** O(V + E) where V = numCourses and E = prerequisites.length. Every vertex and edge is traversed once.
  - **Space Complexity:** O(V + E) for the adjacency list and the recursion call stack (up to V frames deep in the worst case).

- **Pros / Cons:**
  - **Pros:** Elegant backtracking approach, requires no auxiliary queues, and performs well when graph structures are deep rather than wide.
  - **Cons:** Risk of recursion stack overflow (though unlikely under standard LeetCode constraints); slightly trickier to explain states (0, 1, 2) compared to a queue.

---

## Compare Approaches

| Feature | BFS (Kahn's Algorithm) | DFS (Three-State Coloring) |
| :--- | :--- | :--- |
| **Time Complexity** | O(V + E) | O(V + E) |
| **Space Complexity** | O(V + E) | O(V + E) (Call stack can reach O(V)) |
| **Cycle Detection** | Natural (checks if processed course count equals total courses) | Explicit (detects back-edges via "Visiting" state `1`) |
| **Memory overhead** | Needs explicit `indegree` array and Queue | Uses system call stack |

- **Thinking Process Difference:** BFS builds solutions iteratively from the "source nodes" (indegree `0`) up, peeling layers off. DFS dives deep down a single dependency path as far as possible, checking if the path folds back on itself.
- **Trade-offs:** BFS is generally preferred for generating the exact chronological sequence because queue ordering is highly controllable, whereas DFS code is more compact and does not require managing a queue manually.

---

## Interview Recommendation

Start with **Kahn's Algorithm (BFS)**. It is widely considered the standard way to introduce topological sorting because:
1. Identifying nodes with "zero dependencies" is extremely logical and easy for an interviewer to visualize.
2. The concept of "peeling layers" makes it trivial to transition directly to **LeetCode 210: Course Schedule II** (which asks for the actual course order).
3. If asked about alternative approaches, transition into the **DFS 3-state coloring** method, highlighting how it detects a back-edge to check for cycles.

---

## Interview Talking Points / Questions

- **Why choose this approach?**
  - *Answer:* Topological sort is the standard algorithm for solving dependency scheduling problems. It allows us to process dependencies in linear time relative to the number of nodes and edges.
- **Why can't we just use regular 2-state DFS (Visited / Unvisited)?**
  - *Answer:* Because a directed graph can have cross-edges that do not form cycles. If we only track `Visited` vs `Unvisited`, we might incorrectly flag a node visited in a previous, completely separate path traversal as a cycle. The "Visiting" state (or backtracking marker) is critical to verify that the node is on the *current active recursion path*.
- **How would you return the exact sequence of courses to take?**
  - *Answer:* If using the BFS approach, the order in which we pop elements from the queue is a valid topological sort. We can just append each popped course to a list and return it.

---

## Key Takeaway

- **Core Algorithmic Pattern:** Topological Sort / Directed Acyclic Graph (DAG) analysis.
- **Key Techniques:** Dependency-peeling with Indegree tracker, Three-state DFS coloring.
- **Relation to Other Problems:**
  - [LeetCode 210 - Course Schedule II](https://leetcode.com/problems/course-schedule-ii/) *(Exact same concept but returns the ordering list)*
  - [LeetCode 269 - Alien Dictionary](https://leetcode.com/problems/alien-dictionary/) *(Harder version requiring building a graph from letter sorted order)*
  - [LeetCode 310 - Minimum Height Trees](https://leetcode.com/problems/minimum-height-trees/) *(Uses BFS layer-peeling from the leaves)*
