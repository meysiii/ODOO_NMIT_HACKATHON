# ODOO_NMIT_HACKATHON

# Dayflow — Human Resource Management System

> **Every workday, perfectly aligned.**

Dayflow is a full-stack HR workspace built for the ODOO × NMIT hackathon. It brings the complete employee-to-HR workflow together in one secure, colorful, and resilient portal: login-first role-based access, employee self-service, a live HR command center, attendance, leave, payroll, notifications, and an explainable Workforce Pulse — all backed by typed, permission-checked data.

---

## Table of contents

- [Why Dayflow](#why-dayflow)
- [Feature map](#feature-map)
- [Architecture](#architecture)
- [Role permissions](#role-permissions)
- [Local setup](#local-setup)
- [Scripts](#scripts)
- [Testing](#testing)
- [Demo walkthrough](#demo-walkthrough)
- [Roadmap](#roadmap)

---

## Why Dayflow

Most HRMS prototypes stop at CRUD screens. Dayflow adds a **transparent operational intelligence layer** on top of real records:

- **Login-first & role-aware.** No one reaches the portal without signing in. Employee and HR Manager sessions are validated server-side — a client can't just flip a role.
- **Live, not static.** Dashboards and detail views read from typed backend procedures with proper loading, error, and empty states — plus bounded retries and a clearly labeled offline/cached mode when the network drops.
- **Explainable Workforce Pulse.** It never ranks, scores, or diagnoses people. Every signal is a visible cue backed by real attendance/leave evidence, with the final call left to HR.

## Feature map

| Area | Employee experience | HR Manager experience |
|---|---|---|
| **Access** | Sign in with email or employee ID; self-register | Sign in via approved admin account; restricted creation path |
| **Security** | Salted scrypt password hashing, signed sessions | Same, plus role-guarded procedures on every request |
| **Dashboard** | Personal snapshot of attendance, leave, payroll | Live summary cards, activity stream, quick actions |
| **Profile** | View profile; edit address, phone, photo | Full employee directory with search, 50+ seeded records |
| **Attendance** | Check in / check out; calendar & weekly chart | Team attendance visibility |
| **Leave** | Submit paid/sick/unpaid requests; track status | Approve/reject with comments, timestamped review |
| **Payroll** | Read-only salary, HRA, benefits, deductions, net pay | Payroll readiness & management controls |
| **Notifications** | Read/unread state, mark-all-as-read, event-driven alerts | Same, org-wide |
| **Workforce Pulse** | Privacy principles visible by design | Evidence-backed signals, no hidden scoring |
| **Resilience** | Live / Syncing / Offline indicator, cached read-only fallback | Same |
| **Onboarding** | — | Persistent setup checklist for first-time admins |
| **Design** | Light & dark themes, responsive layouts, animated charts | Same |

## Architecture

Dayflow is built on the Manus full-stack template:

- **Client:** React 19 + Tailwind CSS, `wouter` for routing, TanStack Query + tRPC client for typed data fetching
- **Server:** Express + tRPC for fully typed, role-guarded procedures
- **Database:** Drizzle ORM (MySQL) with migrations in `/drizzle`
- **Auth:** Manus OAuth alongside first-party credential login (scrypt-hashed passwords, signed session cookies)

**Schema (`drizzle/schema.ts`):** `users`, `employees`, `attendance`, `leaveRequests`, `payroll`, `notifications`, `pulseSignals`.

**Server procedures (`server/routers.ts`):** auth (sign-in/out, credential accounts), employee directory reads, attendance check-in/out, leave creation & review, payroll reads, dashboard metrics, notifications, and Pulse signals — each gated by `publicProcedure`, `protectedProcedure`, or `adminProcedure`.

```
client/    React app (pages, components, hooks, contexts)
server/    Express + tRPC routers, auth/credentials, db access, tests
shared/    Shared types, constants, and business-rule helpers (+ tests)
drizzle/   Schema, relations, and SQL migrations
```

## Role permissions

**Employees** can read their own profile, attendance, leave requests, and payroll — and edit only address, phone, and profile photo.

**HR Managers** can read the full employee directory, all attendance and leave records, and payroll; they can edit complete employee records and approve/reject leave with comments. Workforce Pulse stays aggregate-first and never exposes sensitive individual inferences.

## Local setup

```bash
pnpm install
pnpm dev
```

The app runs through the local dev server (Vite + Express). Environment variables (database connection, session secret, etc.) are read via `dotenv` — no credentials are committed to the repo.

## Scripts

| Command | Purpose |
|---|---|
| `pnpm dev` | Start the dev server with hot reload |
| `pnpm build` | Build client (Vite) and bundle server (esbuild) |
| `pnpm start` | Run the production build |
| `pnpm check` | TypeScript type-check (`tsc --noEmit`) |
| `pnpm test` | Run the test suite (Vitest) |
| `pnpm format` | Format the codebase with Prettier |
| `pnpm db:push` | Generate and run Drizzle migrations |

## Testing

Vitest coverage includes: authentication & logout behavior, credential sign-in flow, permission boundaries, dashboard contract & metrics, employee directory access, leave/attendance business rules (work-minute calculation, inclusive leave-day counting, overlap protection ignoring rejected requests), offline/cached dashboard behavior, and fetch resilience (retries). `pnpm build` and `pnpm check` are verified for release readiness.

## Demo walkthrough

1. Sign in as **HR Manager** — walk through the overview metrics, open the People directory, and search an employee.
2. Open **Leave & time-off**, approve a pending request, and point out the immediate status update and notification.
3. Open **Payroll** to show the transparent salary breakdown.
4. Open **Workforce Pulse** — explain that a signal is a traceable operational cue, not a hidden score.
5. Switch to the **Employee** view — check in, show the personal dashboard, and open the restricted profile-edit fields.
6. Simulate an offline state to show the cached, read-only fallback and automatic recovery on reconnect.

## Roadmap

A production extension would connect procedures to fully seeded live database records, add email verification and richer notification delivery, introduce downloadable payslips and attendance reports, and support configurable organization policies — all while preserving the same privacy-first, explainable design.

---

Built for the ODOO × NMIT hackathon. **Dayflow moves HR from chasing records to seeing the next helpful action — without turning people into scores.**
