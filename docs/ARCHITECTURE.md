# Architecture

This document explains how the app is put together: state flow, folder layout, and how a new puzzle gets added.

## High-level flow

The whole game runs client-side, in a single page — there's no backend or persistence. Everything lives in React state for the duration of the session.

```
main.tsx
  └── App.tsx            (providers: react-query, tooltip, toaster, router)
       └── Index.tsx      (wraps everything in GameStateProvider)
            ├── RoomHub.tsx   (room selection screen)
            └── Room.tsx      (puzzle list + active puzzle for the current room)
                 └── <puzzle component>  (SudokuPuzzle, BFSPathfinder, LogicPuzzle,
                                          ConnectFour, BlocksWorld)
```

`Index.tsx` decides whether to render `RoomHub` (no room selected yet) or `Room`
(a room is selected) based on `currentRoom` from game state.

## State management: `GameStateContext`

All progression state lives in one context — `src/contexts/GameStateContext.tsx`.
There's no external state library; this is plain `useState` + React Context,
which is enough for a project this size.

It tracks:

- **`rooms`** — an array of `Room` objects (id, name, description, `status`,
  and a list of `puzzles`, each with its own `completed` flag).
- **`currentRoom`** / **`currentPuzzle`** — which screen is currently active.

Room statuses form a simple state machine: `locked → unlocked → completed`.
When `updatePuzzleStatus` marks the last puzzle in a room as completed, the
room itself is marked `completed`, which in turn unlocks the *next* room in
the array via `updateRoomStatus`. This is what creates the escape-room
"clear this room to open the next one" progression — there's no separate
route or page per room, just this status flag controlling what the UI shows.

**Note:** state is entirely in-memory. Refreshing the page resets all
progress — there's no `localStorage`/`sessionStorage` persistence layer.
That would be a natural first extension if you build on this project.

## Puzzle dispatch pattern

`Room.tsx` doesn't use routing for individual puzzles. Instead, it holds a
`currentPuzzle` id and switches on it:

```tsx
switch (currentPuzzle) {
  case 'sudoku':    return <SudokuPuzzle />;
  case 'pathfinder': return <BFSPathfinder />;
  case 'logic':     return <LogicPuzzle />;
  case 'connect4':  return <ConnectFour />;
  case 'blocks':    return <BlocksWorld />;
}
```

Each puzzle component is self-contained: it manages its own local state (the
board, the maze, the current plan, etc.) and only talks to `GameStateContext`
to report `completed: true` once solved. This keeps puzzle logic isolated —
you can read, test, or reuse `ConnectFour.tsx` without needing to understand
`BlocksWorld.tsx` at all.

## Folder layout

```
src/
├── components/
│   ├── layout/          Header, Footer — shared chrome
│   ├── rooms/
│   │   ├── RoomHub.tsx  Room selection screen
│   │   └── Room.tsx     Puzzle list + puzzle dispatch for one room
│   ├── puzzles/
│   │   ├── room1/       Sudoku, BFS Pathfinder, Logic Puzzle
│   │   │   ├── components/   Sub-components specific to room1 puzzles
│   │   │   ├── hooks/         useLogicPuzzle — local puzzle state machine
│   │   │   ├── types/         Shared TS types for room1
│   │   │   └── utils/         Static question data
│   │   ├── room2/       ConnectFour.tsx
│   │   └── room3/       BlocksWorld.tsx + components/, hooks/
│   └── ui/               shadcn/ui primitives (button, card, dialog, etc.)
├── contexts/
│   └── GameStateContext.tsx   Global room/puzzle progression state
├── hooks/                      Generic hooks (use-mobile, use-toast)
├── lib/
│   └── utils.ts                 `cn()` class-merging helper (shadcn convention)
├── pages/
│   ├── Index.tsx                 Entry page: RoomHub vs Room
│   └── NotFound.tsx              404 fallback route
└── utils/                         Pure algorithm logic, no React
    ├── sudokuUtils.ts
    ├── pathfinderUtils.ts
    ├── connectFourUtils.ts
    └── blocksWorldUtils.ts
```

The consistent split is: **`utils/*.ts` holds pure, framework-free algorithm
code** (board generation, solvers, search) while the matching component
under `components/puzzles/` handles rendering and user interaction. This
means the algorithms themselves are unit-testable in isolation and don't
know React exists — see [ALGORITHMS.md](./ALGORITHMS.md) for what each one
actually does.

## Adding a new puzzle

If you want to extend this project with a new puzzle, the pattern is:

1. Add the pure logic in a new `src/utils/<name>Utils.ts` file.
2. Build the puzzle UI as a component under `src/components/puzzles/<room>/`.
3. Register it in the `switch` inside `Room.tsx`.
4. Add its id/title/description to the matching room's `puzzles` array in
   `GameStateContext.tsx`.
5. Call `updatePuzzleStatus(roomId, puzzleId, true)` from your component once
   the player solves it.

That's the entire integration surface — no routing changes needed.

## Styling

Tailwind CSS + shadcn/ui components (Radix UI primitives underneath), with a
dark, terminal/neon aesthetic defined in `src/index.css` and
`tailwind.config.ts` (monospace type, glow effects, neon borders) to match
the "escape room powered by AI" theme.
