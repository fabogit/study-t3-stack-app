# study-t3-stack-app

> A full-stack, end-to-end type-safe web application leveraging the T3 Stack: Next.js 13, tRPC v10, Prisma ORM, NextAuth.js, and Tailwind CSS.

**Inception (First Commit):** `2022-12-09` • **Batch / Horizon:** `batch-2021-2022` • **Architecture Domain:** `Full-Stack Type-Safe Architecture / T3 Stack`

---

## 📌 Architecture & Overview

`t3-stack-app` implements a unified, end-to-end type-safe full-stack architecture built according to create-t3-app principles. It integrates Next.js Pages router, tRPC v10 for RPC client-server data synchronization without code generation, Prisma ORM for relational persistence, NextAuth.js for session authentication, and Tailwind CSS for utility styling.

The architecture eliminates API boundary friction by sharing TypeScript types directly between server routers and clientside React Query hooks, guaranteeing compile-time validation for network calls.

```
+-------------------------------------------------------------------------+
|                               t3-stack-app                              |
|                                                                         |
|  [ React 18 Frontend ]                                                  |
|  (Next.js Pages Router)                                                 |
|         |                                                               |
|         | trpc.example.hello.useQuery({ text: 'world' })                |
|         v                                                               |
|  +--------------------+                                                 |
|  | @trpc/react-query  | (Type-safe client with SuperJSON transform)     |
|  +---------+----------+                                                 |
|            |                                                            |
|            | HTTP POST /api/trpc/example.hello (Batch Link)             |
|            v                                                            |
|  +--------------------+                                                 |
|  | Next.js API Route  | (/api/trpc/[trpc].ts Handler)                   |
|  +---------+----------+                                                 |
|            |                                                            |
|            v                                                            |
|  +--------------------+       Protected Session                         |
|  | tRPC appRouter     |<------------------------------+                 |
|  +---------+----------+                               |                 |
|            |                                  +---------------+         |
|            +-----------------------+          | NextAuth.js   |         |
|            |                       |          | Session Guard |         |
|            v                       v          +---------------+         |
|  +--------------------+  +--------------------+                         |
|  | authRouter         |  | exampleRouter      |                         |
|  | (getSecretMessage) |  | (getAll / hello)   |                         |
|  +--------------------+  +---------+----------+                         |
|                                    |                                    |
|                                    v                                    |
|                          +--------------------+                         |
|                          | Prisma ORM Client  |                         |
|                          | (SQLite / Postgres)|                         |
|                          +--------------------+                         |
+-------------------------------------------------------------------------+
```

* **Context:** Architectural reference implementation evaluating end-to-end type safety, tRPC v10 procedure routers, NextAuth session adapters, and zero-codegen RPC data fetching.
* **Key Patterns:**
  * **End-to-End Type Safety:** Automatic propagation of server router return types to client hooks without intermediate OpenAPI or GraphQL schema compilation.
  * **Contextual Authentication Middleware:** Reusable `protectedProcedure` middleware intercepting incoming tRPC operations and enforcing valid NextAuth sessions.
  * **SuperJSON Data Transformation:** Preserving complex JavaScript primitives (`Date`, `Set`, `Map`) across JSON HTTP payloads.

---

## 🛠 Tech Stack

* **Framework & Core:** Next.js 13.0.2, React 18.2.0, `react-dom` 18.2.0
* **RPC & Data Fetching:** tRPC v10.0.0 (`@trpc/client`, `@trpc/next`, `@trpc/react-query`, `@trpc/server`), TanStack React Query 4.16.0, `superjson` 1.9.1
* **Authentication:** NextAuth.js 4.18.3, `@next-auth/prisma-adapter` 1.0.5
* **Database & ORM:** Prisma ORM 4.5.0, SQLite (development)
* **Styling & Validation:** Tailwind CSS 3.2.0, Zod 3.19.1

---

## ✨ Features & Endpoints

* **tRPC Procedure Pipelines:** Public and protected RPC query execution with automatic input validation via Zod schemas.
* **Authentication Subsystem:** Discord OAuth credential provider integration via NextAuth with database session tracking.
* **Prisma Schema Generation:** Account, Session, User, and VerificationToken models configured for NextAuth adapter storage.
* **Client-Side Devtools:** LoggerLink logging and batch request pipelining.

### Key tRPC Procedures & Endpoints

| Type | Path / Route | Auth Requirement | Description |
|---|---|---|---|
| `QUERY` | `example.hello` | Public | Returns formatted greeting accepting optional string input. |
| `QUERY` | `example.getAll` | Public | Fetches all records from the `Example` Prisma database table. |
| `QUERY` | `auth.getSession` | Public | Returns current authenticated user session metadata. |
| `QUERY` | `auth.getSecretMessage` | Protected | Authenticated-only procedure returning privileged response. |
| `HTTP` | `/api/auth/[...nextauth]` | Public | NextAuth.js OAuth callback and session token handler. |
| `HTTP` | `/api/trpc/[trpc]` | Dynamic | tRPC universal API batch route. |

---

## 🚀 Getting Started

### Prerequisites

* Node.js `>= 16.0.0`
* npm or pnpm

### Installation

```bash
# Clone the repository
git clone https://github.com/fabogit/study-t3-stack-app.git
cd study-t3-stack-app

# Install dependencies
npm install
```

### Environment Configuration

Configure environment variables in `.env` based on `.env.example`:

```env
DATABASE_URL="file:./db.sqlite"
NEXTAUTH_SECRET="super-secure-random-secret-key"
NEXTAUTH_URL="http://localhost:3000"
DISCORD_CLIENT_ID=""
DISCORD_CLIENT_SECRET=""
```

### Database Setup

```bash
# Push schema to SQLite database
npx prisma db push
```

### Running the Application

```bash
# Launch development server
npm run dev

# Build for production
npm run build
npm run start
```

Navigate to [http://localhost:3000](http://localhost:3000).

---

## 📄 License & Author

Developed by [@fabogit](https://github.com/fabogit).
