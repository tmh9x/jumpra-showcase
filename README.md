<div align="center">

# jumpra

**A Next.js SaaS starter kit for EU teams that build with AI coding agents.**
The groundwork every multi-tenant SaaS needs is done – so you jump straight to your product.

`Next.js` · `React` · `TypeScript` · `Tailwind CSS` · `PostgreSQL` · `Drizzle` · `Better Auth` · `Stripe`

</div>

---

> **This is the public showcase repository.** jumpra is a commercial product, so its source code lives in a private repository. This page describes what it includes, how it is built and the engineering decisions behind it.
>
> **Status:** in active development · waitlist coming soon

---

## What it is

Most SaaS products start with the same weeks of work: sign-in, organizations, roles, billing, e-mails, a dashboard, deployment. jumpra ships all of that as a tested, documented monorepo, built so that AI coding agents (Claude Code, Cursor) can extend it safely without breaking tenant isolation or permissions.

## Features

**Always on (core)**
- E-mail/password sign-in, optional Google and GitHub OAuth
- Organizations with roles and invitations
- Multi-tenant data model with an example tenant-scoped resource
- Role-based permissions (RBAC)
- Dashboard UI built on shadcn/ui and Tailwind CSS 4

**Optional modules** – stay off until their environment variables are set
- Stripe billing per organization, with plan limits and entitlements
- AI features, analytics, monitoring
- Operator dashboard

**Built for AI coding agents**
- `AGENTS.md` / `CLAUDE.md` with project rules
- Agent skills, e.g. *add a tenant-scoped resource*, *add a permission*, *add a plan feature*
- Cursor rules pointing to the same conventions

## Quality and security

jumpra treats safety checks as part of the product, not an afterthought:

- **Security test suite in CI** – tenant isolation, RBAC, billing and webhook handling, backed by a documented security catalog that states what is covered and where the limits are
- **Architecture Guard** – one command that runs type-checking, ESLint, import-boundary rules and custom checks, so agents (and humans) can't silently break the architecture
- **Testing pyramid** – Vitest unit and integration tests against a real Postgres, Playwright end-to-end tests against a production build
- **Drift checks** – CI fails if database schema or migrations are out of date, if documented env variables are missing, or if build output is incomplete
- **Validated configuration** – all environment variables are checked with Zod at startup

## Architecture

```mermaid
flowchart LR
  subgraph apps
    web["apps/web<br/>Next.js App Router"]
  end
  subgraph packages
    auth["auth<br/>Better Auth"]
    billing["billing<br/>plans & entitlements"]
    db["database<br/>Drizzle + Postgres"]
    ui["ui<br/>shadcn/ui + Tailwind 4"]
    email["email<br/>React Email + Resend"]
    config["config<br/>Zod env validation, logger"]
  end
  subgraph tooling
    guard["Architecture Guard"]
    eslint["shared ESLint / TS configs"]
  end
  web --> auth & billing & ui & email
  auth --> db
  billing --> db
  web --> config
  guard -. checks .-> web & packages
```

| Area | Choice |
|:--|:--|
| Monorepo | pnpm workspaces + Turborepo |
| App | Next.js (App Router), React, TypeScript |
| UI | shadcn/ui (Base UI), Tailwind CSS 4 |
| Data | PostgreSQL, Drizzle ORM, versioned migrations |
| Auth | Better Auth (e-mail/password, OAuth, organizations) |
| Billing | Stripe, plan config with `getEntitlements()` / `can()` |
| E-mail | React Email templates, Resend adapter, local log adapter |
| Testing | Vitest, Playwright |
| Deployment | Docker reference deployment with Postgres and Caddy |

## Engineering decisions

- **Modules off by default.** Optional features only activate when their variables are set, so a fresh project starts small and you only configure what you use.
- **Tenant isolation is tested, not assumed.** Security tests in CI check that data and permissions stay within an organization.
- **Rules that machines can check.** Conventions are encoded as lint rules and import boundaries rather than only written down – that is what makes AI-assisted development on top of the kit safe.
- **Separate buyer export.** Internal planning and docs stay in the development repository; buyers receive a clean, documented release with a changelog and upgrade notes.

## Built by

**Thomas Hermanu** – Frontend-focused Full-Stack Engineer (React, TypeScript), Ho Chi Minh City
[LinkedIn](https://www.linkedin.com/in/thomashermanu/) · [GitHub](https://github.com/tmh9x)

Happy to walk through the code in an interview – just ask.
