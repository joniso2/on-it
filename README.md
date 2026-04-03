<div align="center">

  <h1>🚀 ON-IT</h1>

  <h3>Operational Management SaaS for Real-Time Ticketing, Workforce Tracking & Automation</h3>

  <p>
    <strong>Real-time ticket lifecycle · SLA enforcement · Automated escalation · Workforce tracking</strong>
    <br />
    Built for Hebrew-speaking, mobile-first operations teams
  </p>

  <br />

  <a href="#-tech-stack"><img src="https://img.shields.io/badge/Next.js-16-000000?style=for-the-badge&logo=next.js&logoColor=white" /></a>
  <a href="#-tech-stack"><img src="https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black" /></a>
  <a href="#-tech-stack"><img src="https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white" /></a>
  <a href="#-tech-stack"><img src="https://img.shields.io/badge/PostgreSQL-Supabase-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" /></a>
  <a href="#-tech-stack"><img src="https://img.shields.io/badge/Vercel-Deployed-000000?style=for-the-badge&logo=vercel&logoColor=white" /></a>

  <br /><br />

  <img src="https://img.shields.io/badge/status-pilot%20deployment-brightgreen?style=flat-square" />
  <img src="https://img.shields.io/badge/language-Hebrew%20RTL-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/mobile--first-responsive-orange?style=flat-square" />
  <img src="https://img.shields.io/badge/license-private-red?style=flat-square" />

</div>

<br />

---

## 📋 Overview

ON-IT manages the full lifecycle of operational tickets — from creation and auto-assignment through SLA enforcement and multi-level escalation to resolution and closure. It tracks daily workforce attendance, generates PDF and Excel reports with Hebrew RTL support, and delivers real-time notifications via in-app alerts and WhatsApp.

The system serves organizations running customer service and technical support operations where response time, accountability, and visibility matter.

> 🟢 **Currently in pilot deployment**

---

## 🎬 Screenshots

<div align="center">
<table>
  <tr>
    <td align="center" colspan="2">
      <strong>🏠 Admin Dashboard — 9 modules at a glance</strong>
      <br /><br />
      <img src="https://github.com/user-attachments/assets/79eb0ee5-0501-4831-acaa-c5b136bd84dc" width="80%" alt="Admin Dashboard" />
    </td>
  </tr>
  <tr>
    <td align="center" width="50%">
      <strong>📋 Ticket Board — filters, stats & SLA tracking</strong>
      <br /><br />
      <img src="https://github.com/user-attachments/assets/5e9ff995-6051-455a-a647-1a5ca3a13bdd" width="100%" alt="Ticket Board" />
    </td>
    <td align="center" width="50%">
      <strong>👥 Attendance Management</strong>
      <br /><br />
      <img src="https://github.com/user-attachments/assets/38c85cf7-3e35-4d3b-9946-03e908143f68" width="100%" alt="Attendance Grid" />
    </td>
  </tr>
  <tr>
    <td align="center" colspan="2">
      <strong>📊 Reports Hub — PDF & Excel export for 7 report types</strong>
      <br /><br />
      <img src="https://github.com/user-attachments/assets/42168e0b-b85a-45ec-bf54-d6673c702ea2" width="80%" alt="Reports" />
    </td>
  </tr>
</table>
</div>

---

## ✨ Core Features

<table>
  <tr>
    <td width="50%" valign="top">

### 🎫 Ticket Lifecycle
- Priority tiers (critical → low) with auto-calculated SLA deadlines
- State machine: **open → assigned → in-progress → resolved → closed**
- Auto-assignment by facility, role, and attendance status
- Bump for urgency · Defer with reason and return date
- Full event timeline · Internal + public comments

</td>
<td width="50%" valign="top">

### ⏱️ Assignment Acknowledgment
- 15-minute acknowledgment window per assignment
- Auto-escalation: team lead → department manager fallback
- Escalation targets filtered by department, status, and attendance
- One active assignment + one pending ACK per ticket — **enforced at the database level**

</td>
  </tr>
  <tr>
    <td width="50%" valign="top">

### 🔔 Notifications
- Dual-channel: in-app + WhatsApp (Twilio)
- WhatsApp failure never blocks in-app delivery
- **7 trigger types:** assignment · SLA escalation · status change · ACK timeout · bump · comment · manager update

</td>
<td width="50%" valign="top">

### 📊 Reports & Exports
- **10 report views** including executive summary, trends, and per-facility breakdown
- Server-side PDF generation with RTL Hebrew templates
- Multi-sheet Excel workbooks with formatted headers
- Auto-generated Hebrew narrative insights

</td>
  </tr>
  <tr>
    <td width="50%" valign="top">

### 👥 Workforce Attendance
- Daily marking: present · absent · present-no-tasks
- Check-in/check-out with session types (regular, overtime, on-call)
- Custom shift time ranges
- **Attendance feeds directly into auto-assignment** — absent = excluded

</td>
<td width="50%" valign="top">

### ⚡ Automated Escalation
- Two independent cron engines running on Vercel
- **SLA escalation:** flags overdue tickets → notifies admins
- **ACK escalation:** detects unacknowledged → routes to supervisors
- Fault-isolated batching · Atomic claims prevent double-escalation

</td>
  </tr>
</table>

<details>
<summary><strong>🔧 Admin & Audit</strong></summary>

<br />

- User CRUD with multi-role assignment (users can hold multiple roles simultaneously)
- Active session viewer with single-session and all-session revocation
- Audit log with entity-level tracking: every security-sensitive action records the user, entity, old/new values, and IP address
- Admin-accessible audit log viewer with entity and date filtering

</details>

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Browser (mobile-first, RTL)                      │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       Next.js Middleware                             │
│  ┌─────────────────┐  ┌──────────────────┐  ┌───────────────────┐  │
│  │ Session Check    │  │ CSRF Generation  │  │ Route Protection  │  │
│  └─────────────────┘  └──────────────────┘  └───────────────────┘  │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│              Server Components + Server Actions                     │
│                                                                     │
│  validate CSRF → check permissions → parse input (Zod)             │
│  → write DB → log audit → send notifications → revalidate cache    │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       Permission Layer                              │
│  ┌─────────────┐  ┌────────────────┐  ┌─────────────────────────┐  │
│  │ 11 Roles     │  │ 37 Permissions │  │ Scope → SQL WHERE       │  │
│  │ 4 Levels     │  │ Per Role       │  │ Row-Level Isolation     │  │
│  └─────────────┘  └────────────────┘  └─────────────────────────┘  │
└────────────────────┬────────────────────────────┬──────────────────┘
                     │                            │
                     ▼                            ▼
┌────────────────────────────────┐  ┌────────────────────────────────┐
│      PostgreSQL (Supabase)     │  │       External Services        │
│                                │  │                                │
│  • 16 tables, 47+ indexes      │  │  📱 Twilio → WhatsApp          │
│  • Drizzle ORM (type-safe)     │  │  📧 Nodemailer → Email         │
│  • Atomic upserts              │  │  ⏰ Vercel Cron → Escalation   │
│  • Unique partial indexes      │  │  📄 Puppeteer → PDF            │
└────────────────────────────────┘  └────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|:---|:---|
| **Frontend** | Next.js 16 (App Router, Server Components), React 19 + React Compiler |
| **Language** | TypeScript 5 (strict mode) |
| **Backend** | Server Actions, Next.js Middleware, Vercel Cron |
| **Database** | PostgreSQL (Supabase) + Drizzle ORM |
| **Styling** | Tailwind CSS 4, shadcn/ui |
| **Validation** | Zod 4 |
| **Notifications** | Twilio (WhatsApp), Nodemailer (email) |
| **Reporting** | Puppeteer (PDF), SheetJS (Excel), Recharts (charts) |
| **Testing** | Playwright (E2E) |
| **Deployment** | Vercel — serverless functions + edge middleware + cron |

<div align="center">

  <br />

  <img src="https://img.shields.io/badge/Next.js_16-000000?style=for-the-badge&logo=next.js&logoColor=white" />
  <img src="https://img.shields.io/badge/React_19-61DAFB?style=for-the-badge&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/TypeScript_5-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=supabase&logoColor=white" />
  <img src="https://img.shields.io/badge/Drizzle-C5F74F?style=for-the-badge&logo=drizzle&logoColor=black" />
  <img src="https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white" />
  <img src="https://img.shields.io/badge/Twilio-F22F46?style=for-the-badge&logo=twilio&logoColor=white" />
  <img src="https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white" />

</div>

---

## ⚙️ Engineering Highlights

<table>
  <tr>
    <td>🔀</td>
    <td><strong>Parallel Query Batching</strong></td>
    <td>Dashboard fires 7 independent DB queries simultaneously — page load equals the slowest query, not the sum</td>
  </tr>
  <tr>
    <td>🔒</td>
    <td><strong>Atomic Distributed Rate Limiting</strong></td>
    <td>PostgreSQL atomic upserts replace Redis — works correctly across multiple serverless instances with zero external state</td>
  </tr>
  <tr>
    <td>🛡️</td>
    <td><strong>Two-Tier Login Protection</strong></td>
    <td>Separate rate limit buckets per IP and per username — blocks single-source brute force and distributed credential stuffing</td>
  </tr>
  <tr>
    <td>🗄️</td>
    <td><strong>Database-Enforced Uniqueness</strong></td>
    <td>Unique partial indexes guarantee one active assignment and one pending ACK per ticket — race conditions are impossible, not just unlikely</td>
  </tr>
  <tr>
    <td>🎯</td>
    <td><strong>Scope-Based SQL Generation</strong></td>
    <td>User permissions compile into SQL WHERE clauses at query time — data isolation in the DB engine, not application memory</td>
  </tr>
  <tr>
    <td>⚡</td>
    <td><strong>Per-Request Auth Memoization</strong></td>
    <td>React <code>cache()</code> wraps auth lookup — called from multiple server components per request, hits the database only once</td>
  </tr>
  <tr>
    <td>🔄</td>
    <td><strong>Fault-Isolated Cron Processing</strong></td>
    <td><code>Promise.allSettled</code> in escalation jobs — if ticket #47 throws, tickets #48–#200 still process</td>
  </tr>
  <tr>
    <td>⚛️</td>
    <td><strong>Atomic Escalation Claims</strong></td>
    <td>Conditional UPDATE claims prevent double-escalation across overlapping cron invocations</td>
  </tr>
  <tr>
    <td>🧠</td>
    <td><strong>React Compiler</strong></td>
    <td>Build-time automatic memoization of components and hooks — zero manual <code>useMemo</code>/<code>useCallback</code></td>
  </tr>
  <tr>
    <td>🌍</td>
    <td><strong>RTL From the Ground Up</strong></td>
    <td>Tailwind logical properties throughout UI, PDF templates, and Excel exports — native right-to-left Hebrew support</td>
  </tr>
</table>

---

## 🔐 Security & Access Control

<table>
  <tr>
    <td width="50%" valign="top">

#### 🔑 Authentication
- Passwords hashed with adaptive algorithm; never stored in plaintext
- Session tokens hashed before storage — DB compromise ≠ valid sessions
- Sliding-window expiry: activity extends, inactivity doesn't
- First login forces password change
- Password reset via time-limited email tokens
- Rate-limited by both IP address and account

</td>
<td width="50%" valign="top">

#### 🎭 Authorization
- **11 roles** across **4 hierarchy levels**
- Users can hold multiple roles simultaneously
- **37 permissions** covering tickets, users, attendance, reports, sessions, audit
- Every DB query filtered by requesting user's scope
- No data leaks through unfiltered queries

</td>
  </tr>
  <tr>
    <td width="50%" valign="top">

#### 🛡️ Request Security
- CSRF validated on every state-changing action (timing-safe)
- CSP, HSTS, X-Frame-Options, X-Content-Type-Options headers
- Cookies: httpOnly · Secure (prod) · SameSite

</td>
<td width="50%" valign="top">

#### 📝 Audit Trail
- All security-sensitive actions logged with user, entity, old/new values, IP
- Admin-accessible audit log viewer with filtering
- Entity-level change tracking

</td>
  </tr>
</table>

---

## 🔄 Example Workflows

### 🎫 Ticket: Critical hardware failure at a facility

```
📝 Dispatcher creates critical ticket for Facility A
      │
      ▼
⏱️  System calculates 30-minute SLA deadline
      │
      ▼
🎯 Auto-assignment: filters eligible + present reps → picks one at random
      │
      ├──→ 📱 In-app notification sent
      ├──→ 💬 WhatsApp message sent (if configured)
      │
      ▼
⏳ 15-minute acknowledgment timer starts
      │
      ├── ✅ Rep acknowledges → moves to in-progress → resolves → manager closes
      │
      ├── ❌ No ACK in 15 min → escalates to team lead
      │         └── Team lead unavailable → escalates to department manager
      │
      └── ⚠️  SLA expires → ticket flagged → all administrators notified
```

<details>
<summary><strong>👥 Attendance → Assignment Integration</strong></summary>

<br />

1. Supervisor opens attendance view (scoped to their team only)
2. Marks 3 of 5 team members as present
3. New ticket created for that facility → only the 3 present members are eligible
4. Team member checks in late via time range → becomes eligible from that point forward

> Attendance status is a **live input** to the auto-assignment algorithm — not just a report.

</details>

---

## 🚀 Deployment

<div align="center">

| | Component | Details |
|:---:|:---|:---|
| ▲ | **Runtime** | Vercel — serverless functions + edge middleware |
| 🐘 | **Database** | Supabase PostgreSQL with connection pooling |
| ⏰ | **Scheduled Jobs** | Vercel Cron — SLA escalation + ACK timeout |
| 🔄 | **Migrations** | Drizzle Kit — version-controlled SQL |
| 🧪 | **Testing** | Playwright E2E — auth flows, navigation, health |
| 📱 | **PWA** | Standalone mobile display, RTL orientation |

</div>

---

## 📊 By the Numbers

| Metric | Value |
|:---|:---:|
| Database Tables | **16** |
| Indexes | **47+** |
| Permissions | **37** |
| Roles | **11** |
| Notification Types | **7** |
| Report Views | **10** |
| Escalation Engines | **2** |

---

<div align="center">

  > **🔒 This repository is private.** Source code access is available upon request for authorized review.

  <br />

  Built with ❤️ for high-performance operations teams

</div>
