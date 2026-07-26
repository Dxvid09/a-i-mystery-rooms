# Algorithms

Each room's puzzle is themed around a real, classic AI/CS algorithm, implemented
from scratch (no external solver libraries) in `src/utils/`. This doc explains
what each one does and where to find it, room by room.

---

## Room 1 — Logic Core

### Neural Network Sudoku
**File:** `src/utils/sudokuUtils.ts`

Despite the flavor-text name, the solver is a classic **backtracking
constraint-satisfaction search** — the standard way to solve Sudoku:

- `isValid(board, row, col, value)` checks the row/column/3x3-box constraints.
- `solveSudoku(board)` recursively finds the next empty cell (`findEmpty`),
  tries digits 1–9, and backtracks whenever a placement leads to a dead end.
- Puzzle generation (`generateSudoku`) works in reverse: fully solve an empty
  board with the same backtracking solver, then remove cells
  (`removeCells`) according to a difficulty setting, leaving a puzzle that's
  guaranteed solvable because it was derived from a valid solution.

**Complexity:** worst-case exponential, but the constraint checks + cell
ordering keep it fast in practice for 9×9 boards, which is why real Sudoku
solvers still use backtracking instead of brute force.

### BFS Pathfinder
**File:** `src/utils/pathfinderUtils.ts`

A grid/maze puzzle solved with **breadth-first search**:

- `findPathBFS(grid, start, end)` explores the maze level-by-level using a
  queue, tracking visited cells and a `cameFrom` map, then reconstructs the
  shortest path once the end cell is reached.
- Because BFS explores in order of distance from the start, the first time it
  reaches `end` is guaranteed to be via the shortest path (on an unweighted
  grid, which this is — every step costs 1).
- `generateMazeWithPath` builds a random maze and verifies a path actually
  exists before showing it to the player, so the puzzle is always solvable.
- `findAlternativePath` is used to show a *different* valid path than the
  player's, for comparison against the AI's route.

**Complexity:** O(V + E) — every cell and its edges are visited once.

### Logic Gate Challenge
**File:** `src/components/puzzles/room1/utils/questionSets.ts` +
`hooks/useLogicPuzzle.tsx`

This one isn't a search algorithm — it's a small quiz engine. Question sets
(classic logic/lateral-thinking riddles like the bat-and-ball problem) are
stored as data, and `useLogicPuzzle` tracks which have been answered
correctly to unlock a password that completes the puzzle.

---

## Room 2 — Strategic Grid

### Connect Four vs AI
**File:** `src/utils/connectFourUtils.ts`

The AI opponent uses **minimax search with alpha-beta pruning** — the
standard approach for two-player, zero-sum, perfect-information games:

- `minimax(board, depth, alpha, beta, maximizingPlayer)` recursively explores
  future moves, alternating between maximizing the AI's score and minimizing
  the human's, up to a fixed depth.
- **Alpha-beta pruning** skips branches that can't possibly change the final
  decision (`if (beta <= alpha) break`), which lets the search go deeper in
  the same amount of time compared to plain minimax.
- **Heuristic evaluation** (`evaluateBoard` / `evaluateWindow`): when the
  search hits its depth limit without a won/lost/drawn position, the board is
  scored by counting how many 2-, 3-, and 4-in-a-row "windows" each player
  has open, with a bonus for center-column control (a well-known Connect
  Four heuristic, since center columns participate in more possible
  4-in-a-rows).
- **Difficulty** maps directly to search depth: easy = 2 ply (plus a chance
  of a random move), medium = 4 ply, hard = 6 ply. Deeper search = the AI
  "sees" further ahead and plays closer to optimally.

**Complexity:** without pruning, minimax is O(b^d) where b ≈ 7 (columns) and
d is the depth; alpha-beta pruning cuts this substantially in practice
(best case closer to O(b^(d/2))), which is why depth 6 is still feasible in
the browser.

---

## Room 3 — Blocks World

### Cognitive Blocks Battle
**File:** `src/utils/blocksWorldUtils.ts`

"Blocks World" is a classic AI planning testbed (used since the early days of
STRIPS-style planning research). The goal: given blocks stacked in some
initial arrangement, find the shortest sequence of moves that reaches a goal
arrangement.

This is solved with **A\* search over the space of block-world states**:

- Each search node is a full board state (`stacks`) plus the sequence of
  `Action`s taken to reach it, its path cost `g` (moves so far), and priority
  `f = g + h`.
- `heuristic(currentState, goalState)` counts misplaced blocks — how many
  blocks are not yet in their correct goal position — as an **admissible
  heuristic** (it never overestimates the true remaining cost, since at
  minimum every misplaced block needs at least one move), which is what
  guarantees A* finds an optimal (shortest) plan.
- `findPlan` maintains an open set of candidate states, always expanding the
  lowest-`f` node next, and a `visited` set (keyed by `JSON.stringify(state)`)
  to avoid re-exploring the same arrangement twice.
- Every possible "move top block of stack A to top of stack B" transition is
  generated at each step (`applyMove`), and the search terminates the moment
  a state matching the goal is popped.

**Complexity:** the state space grows combinatorially with the number of
blocks, which is why `findPlan` takes a `maxSteps` cap — without the
heuristic pruning search space via `f = g + h`, this would be an impractical
brute-force search.

---

## Why these particular algorithms

Each was picked to be a small, self-contained, genuinely correct
implementation of a foundational search/AI technique — rather than a stub —
so the "compete against the AI" framing of each room is backed by an actual
algorithm doing real work:

| Room | Puzzle | Algorithm | Category |
|---|---|---|---|
| 1 | Sudoku | Backtracking / constraint satisfaction | Search |
| 1 | Pathfinder | Breadth-first search (BFS) | Graph search |
| 2 | Connect Four | Minimax + alpha-beta pruning | Adversarial search |
| 3 | Blocks World | A* search | Informed/heuristic search |

If you're using this project to learn: `pathfinderUtils.ts` is the gentlest
starting point (BFS is the simplest of the four), and `connectFourUtils.ts`
is the best next step into adversarial/game-tree search.
