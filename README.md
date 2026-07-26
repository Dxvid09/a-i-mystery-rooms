# AI Escape Room

An interactive escape-room game where you compete against AI algorithms across a series of puzzle rooms, built with React, TypeScript, and shadcn/ui.

**[Live demo](https://a-i-mystery-rooms.vercel.app)**

## About

Three rooms, each built around a real, classic AI search algorithm — you solve
the puzzle yourself, then see it compared against (or played out by) the
algorithm behind it:

| Room | Puzzles | AI technique |
|---|---|---|
| **Logic Core** | Neural Network Sudoku, BFS Pathfinder, Logic Gate Challenge | Backtracking / constraint satisfaction, breadth-first search |
| **Strategic Grid** | Connect Four vs AI | Minimax with alpha-beta pruning |
| **Blocks World** | Cognitive Blocks Battle | A* search |

Clearing all the puzzles in a room unlocks the next one; clear all three
rooms and you escape. Full write-up of how each algorithm works is in
[`docs/ALGORITHMS.md`](./docs/ALGORITHMS.md).

## Tech Stack

- [React](https://react.dev/) + [TypeScript](https://www.typescriptlang.org/)
- [Vite](https://vitejs.dev/)
- [shadcn/ui](https://ui.shadcn.com/) + [Radix UI](https://www.radix-ui.com/)
- [Tailwind CSS](https://tailwindcss.com/)
- [React Router](https://reactrouter.com/)
- [TanStack Query](https://tanstack.com/query/latest)

## Getting Started

```sh
# Install dependencies
npm install

# Start the dev server
npm run dev

# Build for production
npm run build
```

The dev server runs at `http://localhost:8080` by default (see `vite.config.ts`).

## Project Structure

```
src/
├── components/
│   ├── layout/     # Header, footer
│   ├── puzzles/    # Puzzle logic and UI for each room
│   ├── rooms/      # Room hub and room shell
│   └── ui/         # shadcn/ui components
├── contexts/       # App-wide state (room/puzzle progression)
├── hooks/          # Custom hooks
├── pages/          # Route-level pages
└── utils/          # Pure algorithm implementations (BFS, minimax, A*, backtracking)
```

For a deeper look at how state flows through the app and how a puzzle gets
wired in, see [`docs/ARCHITECTURE.md`](./docs/ARCHITECTURE.md).

## Documentation

- [`docs/ARCHITECTURE.md`](./docs/ARCHITECTURE.md) — state management, folder layout, how to add a new puzzle
- [`docs/ALGORITHMS.md`](./docs/ALGORITHMS.md) — what each room's AI actually does, algorithm by algorithm

## Deployment

This project builds as a static site (`npm run build` → `dist/`) and can be
deployed to any static host — it's currently deployed on
[Vercel](https://vercel.com/) with zero extra configuration (Vercel
auto-detects the Vite preset).

## License

No license file is currently included — all rights reserved by default. Add
a `LICENSE` file (e.g. MIT) if you want to allow reuse.
