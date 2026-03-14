# Velox — Context Anchor
> Last updated: 2026-03-12 14:55 | Session: Task API complete, starting frontend components

---

## 🚀 Resume Prompt

You are working on **Velox**, a React + Fastify task management app. The backend is now feature-complete: auth (JWT + httpOnly cookies) and full task CRUD are implemented and tested. We are now building the React frontend — starting with the `TaskList` component which fetches from `GET /tasks` and renders tasks with inline status toggling. Use React Query for server state. **Watch out:** the API returns soft-deleted tasks in the response by default — the frontend must filter where `deletedAt === null`.

---

## 📍 Current Status
**Working on:** React `TaskList` component  
**Blocked by:** Nothing  
**Next up:** Create `src/components/TaskList.tsx`, wire up React Query `useQuery` for `GET /tasks`, render tasks with a status toggle button

---

## 🗺️ Project Overview

**Purpose:** Lightweight task management app for small teams  
**Stack:** React 18 + React Query (frontend), Fastify 4 (API), Prisma + PostgreSQL (data), Zod (validation), JWT (auth)  
**Structure:**
- `src/auth/` — ✅ Complete
- `src/tasks/` — ✅ Complete. GET/POST/PATCH/DELETE /tasks endpoints
- `src/components/` — 🔧 In progress. React components
- `src/server.ts` — Fastify instance. Plugin order load-bearing.
- `prisma/schema.prisma` — `User`, `Task` models

---

## 📋 Active Work

### Frontend — Task Components
- **Goal:** Build the core task management UI
- **Approach:** React Query for server state, component-per-feature structure
- **Status:** Starting `TaskList` component
- **Key decisions made:**
  - React Query over raw `useEffect` fetching — *caching, refetch on focus, loading states for free*
  - Optimistic updates for status toggle — *snappier UX, roll back on error*
- **Notes:** API returns ALL tasks including soft-deleted — filter `deletedAt === null` on the frontend

---

## ✅ Resolved

### Full Task CRUD API ✓
- **What was done:** GET (list + single), POST (create), PATCH (update), DELETE (soft delete) for tasks
- **Key decision:** Soft delete via `deletedAt` — future undo capability
- **Outcome:** `src/tasks/tasks.ts` complete. All routes auth-protected. Zod validation on all inputs.

### JWT Authentication Flow ✓
- **What was done:** Login, logout, refresh endpoints. 15min access + 7 day refresh tokens.
- **Key decision:** httpOnly cookies — XSS protection
- **Outcome:** `src/auth/auth.ts` complete and tested.

---

## 📝 Decision Log

| Decision | Chosen approach | Reason | Date |
|----------|----------------|--------|------|
| Token storage | httpOnly cookies | XSS protection | 2026-03-12 |
| API framework | Fastify | Schema-first, better TS support | 2026-03-12 |
| ORM | Prisma | Type-safe queries | 2026-03-12 |
| Task deletion | Soft delete (`deletedAt`) | Future undo capability | 2026-03-12 |
| Frontend state | React Query | Caching + optimistic updates | 2026-03-12 |
| Status update UX | Optimistic updates | Snappier UX, roll back on error | 2026-03-12 |

---

## ⚠️ Gotchas & Known Issues

- **API returns soft-deleted tasks** — always filter `deletedAt === null` on the frontend
- **Plugin order in `server.ts` is load-bearing** — auth middleware must register AFTER CORS plugin
- `auth.ts` uses custom session interface — don't use standard `@fastify/jwt` typings
- Dev server requires `--legacy-peer-deps`

---

## 🗄️ Archive

<details>
<summary>Archived items (click to expand)</summary>

*(Resolved section still manageable — nothing archived yet)*

</details>
