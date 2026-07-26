# AI Escape Room

An interactive escape-room game where you compete against AI algorithms across a series of puzzle rooms, built with React, TypeScript, and shadcn/ui.

## About

Each room challenges you to solve a puzzle and then compares your approach against an AI-driven solution:

- **Logic Puzzles** — work through a set of logic questions to unlock the next stage.
- **Pathfinding** — find your own route through a grid and see how it stacks up against an AI pathfinding algorithm.
- **Blocks World** — rearrange a stack of blocks to match a goal state, with an AI solver showing its own solution.

Progress and points carry across rooms, with a completion screen and medal once all rooms are cleared.

## Tech Stack

- [React](https://react.dev/) + [TypeScript](https://www.typescriptlang.org/)
- [Vite](https://vitejs.dev/)
- [shadcn/ui](https://ui.shadcn.com/) + [Radix UI](https://www.radix-ui.com/)
- [Tailwind CSS](https://tailwindcss.com/)
- [React Router](https://reactrouter.com/)
- [TanStack Query](https://tanstack.com/query/latest)

## Getting Started

\`\`\`sh
# Install dependencies
npm install

# Start the dev server
npm run dev

# Build for production
npm run build
\`\`\`

## Project Structure

\`\`\`
src/
├── components/
│   ├── layout/     # Header, footer
│   ├── puzzles/    # Puzzle logic and UI for each room
│   ├── rooms/      # Room hub and room shell
│   └── ui/         # shadcn/ui components
├── contexts/       # App-wide state
├── hooks/          # Custom hooks
├── pages/          # Route-level pages
└── utils/          # Puzzle/game logic and helpers
\`\`\`

## Deployment

This project is set up to build as a static site (\`npm run build\`) and can be deployed to any static host, e.g. [Vercel](https://vercel.com/).
