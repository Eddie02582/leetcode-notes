# LeetCode 37 – Sudoku Solver (Optimized)

## Problem Summary
Fill the empty cells of a 9×9 Sudoku board so that each row, column, and each 3×3 sub-grid contains digits **1–9 exactly once**. The solution must modify the board **in-place**.

---

## Core Difficulty
- Constraint checking (row / column / 3×3 sub-grid)
- Efficient backtracking design with pruning
- Using Minimum Remaining Value (MRV) heuristic

---

## Key Insight
- Maintain **sets for rows, columns, and boxes** to check constraints in O(1)
- Track all **empty cells** and dynamically select the one with **fewest options** (MRV heuristic)
- Backtracking fills empty cells and reverts state on failure

---

## Backtracking Framework with MRV
```python
from typing import List

class Solution:
    def solveSudoku(self, board: List[List[str]]) -> None:
        row = [set() for _ in range(9)]
        col = [set() for _ in range(9)]
        box = [set() for _ in range(9)]
        empties = []

        # Initialize sets and empty positions
        for r in range(9):
            for c in range(9):
                if board[r][c] == ".":
                    empties.append((r, c))
                else:
                    ch = board[r][c]
                    row[r].add(ch)
                    col[c].add(ch)
                    box[(r//3)*3 + c//3].add(ch)

        def get_options(r, c):
            b = (r//3)*3 + c//3
            return set("123456789") - row[r] - col[c] - box[b]

        def backtracking():
            if not empties:
                return True

            # MRV heuristic: choose the empty cell with fewest options
            r, c = min(empties, key=lambda x: len(get_options(x[0], x[1])))
            empties.remove((r, c))

            for ch in get_options(r, c):
                b = (r//3)*3 + c//3
                board[r][c] = ch
                row[r].add(ch)
                col[c].add(ch)
                box[b].add(ch)

                if backtracking():
                    return True

                board[r][c] = "."
                row[r].remove(ch)
                col[c].remove(ch)
                box[b].remove(ch)

            empties.append((r, c))
            return False

        backtracking()
```

---

## Complexity Analysis
- **Time:** Exponential in worst case, but MRV and pruning significantly reduce practical search space
- **Space:** `O(9*3)` for sets + `O(k)` recursion depth, where `k` = number of empty cells

---

## Interview Talking Points
- MRV heuristic improves efficiency over naive DFS
- Using sets for O(1) constraint checking
- Backtracking template applies to other CSP problems
- Comparison with simpler DFS + validity checks

---

## Key Takeaways
- Optimal Sudoku solver combines **backtracking + constraint propagation + MRV**
- Preprocessing empty cells and sets is crucial
- Template is useful for other constraint satisfaction problems

