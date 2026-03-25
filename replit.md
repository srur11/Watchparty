# Workspace

## Overview

pnpm workspace monorepo using TypeScript. Each package manages its own dependencies.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)
- **Realtime backend**: Firebase Realtime Database

## Artifacts

### `artifacts/watch-party` — Watch Party App

A browser-based synchronized watch party system.

**Features:**
- Host creates a room with a video URL and optional password
- Guests join via 6-character room code
- Real-time video synchronization (play/pause/seek) via Firebase
- Real-time chat with per-user colors
- Overlay chat bubbles on top of the video (4s fade)
- Fullscreen support
- Guest count in header
- Host controls playback; guests watch only

**Firebase paths used:**
- `rooms/{roomId}` — room metadata (host, videoUrl, password, createdAt)
- `rooms/{roomId}/videoState` — { playing, time, updatedAt }
- `rooms/{roomId}/chat` — { username, text, timestamp } per message
- `rooms/{roomId}/guests` — joined guests

**Pages:**
- `/` → `src/pages/Home.tsx` — Create or join a room
- `/room/:id` → `src/pages/Room.tsx` — The watch party room

**Firebase env vars required (all set as secrets):**
- `VITE_FIREBASE_API_KEY` (uses `GOOGLE_API_KEY` secret)
- `VITE_FIREBASE_AUTH_DOMAIN`
- `VITE_FIREBASE_DATABASE_URL`
- `VITE_FIREBASE_PROJECT_ID`
- `VITE_FIREBASE_STORAGE_BUCKET`
- `VITE_FIREBASE_MESSAGING_SENDER_ID`
- `VITE_FIREBASE_APP_ID`

## Structure

```text
artifacts-monorepo/
├── artifacts/              # Deployable applications
│   ├── api-server/         # Express API server
│   └── watch-party/        # Watch Party React app (Firebase-powered)
├── lib/                    # Shared libraries
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   └── db/                 # Drizzle ORM schema + DB connection
├── scripts/                # Utility scripts
├── pnpm-workspace.yaml
├── tsconfig.base.json
├── tsconfig.json
└── package.json
```

## TypeScript & Composite Projects

Every package extends `tsconfig.base.json` which sets `composite: true`. The root `tsconfig.json` lists all packages as project references.

Root commands:
- `pnpm run typecheck` — canonical full check
- `pnpm run build` — runs typecheck then builds all packages
