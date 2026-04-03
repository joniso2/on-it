<div align="center">
  <img src="public/onit-logo.svg" alt="ON-IT Logo" width="80" />
  <h1>ON-IT</h1>
  <p><strong>Operational Incident & Workforce Management System</strong></p>
  <p>
    Real-time ticket lifecycle management, workforce attendance tracking, and operational reporting — built for Hebrew-speaking, mobile-first teams.
  </p>
  <p>
    <img alt="Next.js 16" src="https://img.shields.io/badge/Next.js-16-black?logo=next.js" />
    <img alt="React 19" src="https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black" />
    <img alt="TypeScript 5" src="https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white" />
    <img alt="PostgreSQL" src="https://img.shields.io/badge/PostgreSQL-Supabase-336791?logo=postgresql&logoColor=white" />
    <img alt="Vercel" src="https://img.shields.io/badge/Deployed-Vercel-black?logo=vercel" />
  </p>
</div>

---

## Overview

ON-IT manages the full lifecycle of operational tickets — from creation and auto-assignment through SLA enforcement and multi-level escalation to resolution and closure. It tracks daily workforce attendance, generates PDF and Excel reports with Hebrew RTL support, and delivers real-time notifications via in-app alerts and WhatsApp.

The system serves organizations running customer service and technical support operations where response time, accountability, and visibility matter. Currently in pilot deployment.

---

## Core Features

### Ticket Lifecycle

- Tickets carry priority (4 tiers), category, facility, requester contact info, and asset reference
- Dispatchers create tickets and either hand-pick a representative or let the system auto-assign one
- Auto-assignment filters representatives by facility eligibility, role type, and current-day attendance status — then picks randomly from the eligible pool
- Each priority tier maps to a specific SLA deadline (30 minutes for critical up to 24 hours for low)
- Tickets move through a defined state machine: **open → assigned → in-progress → resolved → closed**
- Reporters can "bump" a ticket to signal urgency; dispatchers can defer tickets with a reason and return date
- Full event timeline and comment threads (internal and public) per ticket

### Assignment Acknowledgment

- When a ticket is assigned, the representative gets 15 minutes to acknowledge
- If unacknowledged, the system escalates to the direct team lead; if unavailable, it falls back to the department manager
- Escalation targets are filtered by same department, active status, and not marked absent today
- Only one active assignment and one pending acknowledgment can exist per ticket — enforced at the database level, not in application logic

### Automated Escalation

- Two independent cron jobs run on scheduled intervals:
  - **SLA escalation**: flags overdue tickets and notifies all administrators
  - **ACK escalation**: detects unacknowledged assignments and routes to supervisors with a fallback chain
- Each job processes tickets in batch with fault isolation — one failure does not abort the rest
- Escalation claims are atomic: concurrent invocations cannot double-escalate the same ticket

### Notifications

- Every notification creates an in-app record; if WhatsApp is configured and the user has a phone number, a parallel WhatsApp message is sent via Twilio
- WhatsApp failure does not block the in-app notification
- 7 trigger types: assignment, SLA escalation, status change, ACK timeout, bump, comment, manager update

### Workforce Attendance

- Supervisors mark team members daily as present, absent, or present-no-tasks
- Check-in/check-out sessions track arrival and departure times with support for regular, overtime, and on-call types
- Custom time ranges allow shift-specific availability windows
- Attendance data feeds directly into auto-assignment — absent personnel are excluded from ticket routing

### Reports & Exports

- 10 report views: executive summary, by-status, by-representative, ticket creators, by-building, by-category, attendance, overview, summary, and trends
- PDF reports generated server-side with custom RTL Hebrew templates
- Excel exports produce multi-sheet workbooks with formatted headers and RTL support
- Executive reports include auto-generated Hebrew narrative insights covering top categories, resolution rates, and busiest facilities

### Admin

- User CRUD with multi-role assignment (users can hold multiple roles simultaneously)
- Active session viewer with single-session and all-session revocation
- Audit log with entity-level tracking: every security-sensitive action records the user, entity, old/new values, and IP address

---

## System Architecture

```
Browser (mobile-first, RTL)
  │
  ▼
Next.js Middleware
  • Validates session cookie on every request
  • Generates CSRF token as httpOnly cookie
  • Redirects unauthenticated users to login
  │
  ▼
Server Components + Server Actions
  • Pages render server-side with scope-filtered data
  • Mutations: validate CSRF → check permissions → parse input (Zod)
    → write DB → log audit → send notifications → revalidate cache
  │
  ▼
Permission Layer
  • 11 roles across 4 hierarchy levels
  • 37 granular permissions mapped per role
  • Scope filters translate user context into SQL WHERE clauses
  • Queries return only rows the user is authorized to see
  │
  ▼
PostgreSQL (Supabase)                    External Services
  • 16 tables, 47+ indexes               • Twilio → WhatsApp
  • Drizzle ORM (type-safe queries)       • Nodemailer → Email
  • Atomic upserts for rate limiting      • Vercel Cron → Escalation
  • Unique partial indexes for            • Puppeteer → PDF rendering
    assignment/ACK constraints
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16 (App Router, Server Components, Server Actions) |
| UI | React 19 with React Compiler |
| Language | TypeScript 5 (strict) |
| Database | PostgreSQL on Supabase, Drizzle ORM |
| Styling | Tailwind CSS 4, shadcn/ui |
| Validation | Zod 4 |
| Notifications | Twilio (WhatsApp), Nodemailer (email) |
| PDF Generation | Puppeteer with custom RTL templates |
| Excel Export | SheetJS |
| Charts | Recharts |
| Testing | Playwright (E2E) |
| Deployment | Vercel (serverless functions + edge middleware + cron) |

---

## Engineering Highlights

- **Parallel query batching** — Dashboard page fires 7 independent database queries simultaneously; page load equals the slowest query, not the sum of all
- **Atomic distributed rate limiting** — Login rate limiter uses PostgreSQL atomic upserts, so it works correctly across multiple serverless instances without Redis or external state
- **Two-tier login protection** — Separate rate limit buckets per IP address and per username, blocking both single-source brute force and distributed credential stuffing
- **Database-enforced uniqueness** — Unique partial indexes guarantee one active assignment and one pending acknowledgment per ticket; race conditions between concurrent requests are impossible, not just unlikely
- **Scope-based SQL generation** — User permissions compile into SQL WHERE clauses at query time; data isolation happens in the database engine, not in application memory
- **Per-request auth memoization** — Auth lookup is wrapped in React `cache()` — called from multiple server components in one request, but hits the database only once
- **Fault-isolated cron processing** — Escalation jobs use `Promise.allSettled`; if one ticket escalation fails, the remaining batch still processes
- **Atomic escalation claims** — ACK escalation uses conditional updates to claim a ticket; two overlapping cron invocations cannot double-escalate
- **React Compiler** — Enabled at build time for automatic memoization of components and hooks
- **RTL from the ground up** — Tailwind logical properties throughout the UI; PDF templates and Excel exports also render right-to-left with full Hebrew support

---

## Security & Access Control

### Authentication

- Passwords hashed with adaptive algorithm; never stored or transmitted in plaintext
- Session tokens hashed before storage — database compromise does not yield valid sessions
- Sessions expire on a sliding window; activity extends the session, inactivity does not
- First login forces password change; password reset uses time-limited email tokens
- Login rate-limited by both IP address and account

### Authorization

- 11 roles across 4 hierarchy levels; users can hold multiple roles
- 37 permissions control access to tickets, users, attendance, reports, sessions, and audit
- Every database query is filtered by the requesting user's scope — no data leaks through unfiltered queries

### Request Security

- CSRF tokens validated on every state-changing action using timing-safe comparison
- Content Security Policy, HSTS, X-Frame-Options, and X-Content-Type-Options headers on all responses
- Cookies are httpOnly, Secure in production, SameSite

### Audit Trail

- All security-sensitive actions logged with user, entity, old/new values, and IP address
- Admin-accessible audit log viewer with entity and date filtering

---

## Example Workflows

### Ticket: Critical hardware failure at a facility

1. A dispatcher creates a ticket tagged as critical priority for a specific facility
2. The system calculates a 30-minute SLA deadline
3. Auto-assignment filters to representatives eligible for that facility who are marked present today, then picks one at random
4. The representative receives an in-app notification and a WhatsApp message
5. A 15-minute acknowledgment timer starts
6. The representative acknowledges and moves the ticket to in-progress
7. The ticket is resolved within SLA; a manager reviews and closes it
8. If the representative had not acknowledged — the system escalates to their team lead, then to the department manager
9. If the SLA had expired — the ticket is flagged and all administrators are notified

### Attendance feeding into assignment

1. A supervisor opens the attendance view, scoped to their team only
2. They mark 3 of 5 team members as present
3. When a new ticket is created for that team's facility, only the 3 present members are eligible for auto-assignment
4. If a team member checks in late via a time range entry, they become eligible from that point forward

---

## Deployment

- Serverless deployment on Vercel (functions + edge middleware)
- PostgreSQL hosted on Supabase with connection pooling
- Two scheduled cron jobs for automated escalation (SLA + acknowledgment timeout)
- Database schema managed via Drizzle Kit migrations (version-controlled SQL)
- E2E tests via Playwright covering authentication flows, navigation, and health checks
- PWA manifest configured for standalone mobile display with RTL orientation

---

<div align="center">
  <strong>16</strong> database tables · <strong>47+</strong> indexes · <strong>37</strong> permissions · <strong>11</strong> roles · <strong>7</strong> notification types · <strong>10</strong> report views · <strong>2</strong> automated escalation engines
</div>

---

> **This repository is private.** Source code access is available upon request for authorized review.
