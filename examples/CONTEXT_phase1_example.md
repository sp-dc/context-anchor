# Velox — Context Anchor
> Last updated: 2026-03-12 11:42 | Session: Auth complete, moving to task API

---

## 🚀 Resume Prompt

You are working on **Velox**, a React + Fastify task management SaaS app. We are at the very beginning — the project scaffold is in place and the immediate focus is implementing JWT authentication with refresh tokens stored in httpOnly cookies. The auth logic will live in `src/auth/`. No endpoints are built yet; start by reviewing `src/auth/auth.ts` which has placeholder stubs.

---

## 📍 Current Status
**Working on:** JWT authentication flow  
**Blocked by:** Nothing  
**Next up:** Implement `createSession` and `validateToken` in `src/auth/auth.ts`, then wire up login/logout endpoints in Fastify

---

## 🗺️ Project Overview

**Purpose:** A lightweight task management app for small teams, with a focus on fast load times and minimal UI friction  
**Stack:** React 18 (frontend), Fastify 4 (API), Prisma + PostgreSQL (data), Zod (validation), JWT (auth)  
**Structure:**
- `src/auth/` — authentication logic (JWT, sessions)
- `src/tasks/` — task CRUD (not yet built)
- `src/server.ts` — Fastify instance and plugin registration
**Repo/Location:** `/home/user/projects/velox-app`

---

## 📋 Active Work

### JWT Authentication Flow
- **Goal:** Implement login, logout, and token refresh with httpOnly cookies
- **Approach:** Access token (15min TTL) + refresh token (7 day TTL) stored in httpOnly cookie
- **Status:** In progress
- **Key decisions made:**
  - httpOnly cookies over localStorage — *because localStorage is vulnerable to XSS*
  - Fastify over Express — *because of its schema-first approach and better TypeScript support*
- **Notes:** Plugin registration order in `server.ts` is important — auth middleware must register after CORS plugin or preflight requests fail

---

## ✅ Resolved

*(Nothing completed yet — project just started)*

---

## 📝 Decision Log

| Decision | Chosen approach | Reason | Date |
|----------|----------------|--------|------|
| Token storage | httpOnly cookies | XSS protection; localStorage unsafe for auth tokens | 2026-03-12 |
| API framework | Fastify | Schema validation built-in, superior TS support vs Express | 2026-03-12 |
| ORM | Prisma | Type-safe queries, good migration tooling | 2026-03-12 |

---

## ⚠️ Gotchas & Known Issues

- Plugin registration order in `server.ts` must be preserved — auth middleware breaks if registered before CORS
- Dev server requires `--legacy-peer-deps` due to a peer conflict in the current lockfile

---

## 🗄️ Archive

<details>
<summary>Archived items (click to expand)</summary>

*(Nothing archived yet)*

</details>
