# {{PRODUCT_NAME}} — 12-Week Pre-Development Planning Roadmap

> **Status:** Draft — modify anytime
> **Contributors:** {{@handle}}, {{@handle}}
> **Started:** {{DATE}}   **Planning ends:** {{DATE}}

---

## How to use this template

This is a **pre-development** roadmap. It ends the day before you write production code.

Fill in every `{{PLACEHOLDER}}`. Delete every section that genuinely doesn't apply to your project — but delete it *deliberately*, and write one line saying why. A skipped section you decided to skip is fine; a skipped section you forgot about becomes rework in Month 4.

**Adapting the length.** Twelve weeks assumes a small team working part-time on something with real users and real data. Scale it:

| Project shape | Suggested compression |
|---|---|
| Solo, weekend project, no user data | Run W1, W2, W5, W6 only — about 3 weeks |
| 2 people, part-time, public launch planned | Full 12 weeks as written |
| Funded team, full-time | Compress to 6 weeks; same deliverables, two per week |
| Rebuilding something that already exists | Skip W1; spend the time on W9 specs instead |

**The rule that makes this work:** every week ends with a written artefact that someone outside the team could read. Not a conversation, not a shared understanding — a file in the repo. If a week ends without its artefact, that week didn't happen.

**Placeholder key**

- `{{PRODUCT_NAME}}` — what you're building
- `{{PRIMARY_USER}}` — the one person type v1 serves
- `{{CORE_JOB}}` — the single job they hire your product to do
- `{{DOMAIN}}` — your domain name
- `{{JURISDICTION}}` — whose data protection law applies to you

---
---

# MONTH 1 — Research & Foundational Decisions
### Weeks 1–4 | Understand the problem before touching architecture

Most projects fail on wrong assumptions baked in early, not on bad code. Month 1 exists to eliminate assumptions. Every question answered now prevents a week of rework later.

---

## Week 1 — Know Your User

Interviews and observation. Not documents. Talk to at least **five** real people who match `{{PRIMARY_USER}}` — five is where patterns start repeating and you stop being surprised.

### Research: Workflow Audit

Map how `{{PRIMARY_USER}}` does `{{CORE_JOB}}` **today**, without your product.

- What does the end-to-end workflow look like, step by step, start to finish?
- How often do they do it — daily, weekly, once a term, once a year?
- At what volume? (items per session, per week, per year)
- Which step takes the longest? Which step do they hate most? These are often not the same step, and the difference matters.
- What tools are already in that workflow, and which ones are non-negotiable to them?
- Where does the output go afterwards — who else sees it, in what format?
- What device and context do they work in? Desk, phone, on the move, offline?
- What's their tolerance for a new tool — will they upload data to a web service, or does that need a conversation?
- What would make them pay? What would make them refuse outright?

### Research: Competitive Landscape

- Who already solves this? List every tool, free and paid, including the ugly ones people actually use.
- What do users *hate* about each? Go where complaints are unfiltered — reviews, Reddit, forums, issue trackers, support threads.
- What does each charge, and what's in each tier?
- Where's the gap between free and paid? That gap is often the product.
- Are there local or regional players you'd miss if you only looked at the global market?
- What integrations do users already assume any serious tool will have?
- **What's the "good enough" incumbent?** Usually a spreadsheet, a group chat, or a script someone wrote. This is your real competition, not the funded startup.

### Deliverable — end of Week 1

A **1-page persona document**: who they are, their context, tech comfort, biggest pain, what success looks like for them, and what would make them abandon your product.

Both contributors must agree on this persona. It governs every design decision for the next three months.

---

## Week 2 — Product Scope Decision

The most dangerous failure mode for a small team is scope creep. This week you make hard calls. Everything cut goes on a backlog, not in the bin.

### Decision vocabulary

Use exactly these labels — vagueness here is how scope creeps back in:

| Label | Means |
|---|---|
| **v1 core** | Ship-blocking. Without it there is no product. |
| **v1 basic** | Ships in v1, deliberately unpolished. Define what "basic" means in one line. |
| **v2** | Backlog, next release. Named, not built. |
| **v3+** | Someday. Recorded so it stops being re-litigated. |
| **Cut** | Explicitly not doing this, with a reason. |

### Feature scope table

| Feature | Decision | Reason | If v1 basic — what "basic" means |
|---|---|---|---|
| {{feature}} | {{label}} | {{why}} | {{scope line}} |
| {{feature}} | {{label}} | {{why}} | {{scope line}} |
| {{feature}} | {{label}} | {{why}} | {{scope line}} |

**Prompts to make sure you covered the boring things.** These get forgotten and then block launch:

- How do users get their data *out*? (export, download, API)
- How do users get their data *in* at scale? (bulk import, not one at a time)
- What happens when a long operation is running — how does the user know?
- What does an empty account look like on day one?
- Who can see whose data? Sketch the permission model even if v1 has one role.
- What does the user see when something fails?

### Deliverable — end of Week 2

A **signed-off v1 feature list**. Both contributors put their name on it.

**The trade rule:** nothing is added to v1 after sign-off without removing something of equal or greater scope. Write that rule at the top of the file.

---

## Week 3 — Stack Validation (Hands-On)

You've discussed the stack. This week you validate it by running code. Both contributors complete every spike independently, so you both know the stack — not one person per component.

### How to run a spike

For each technology, timebox it to **half a day**. Write down three things: how long setup actually took, what surprised you, and what would be painful at 100× the scale.

### Spike checklist

Fill in the left column with your actual stack. The right column is what "validated" means.

| Component | Spike — you have validated it when you have… |
|---|---|
| Backend framework | A running server with one endpoint that accepts the hardest input type your product needs (file upload, streaming, websocket) |
| Frontend framework | A project from scratch with one real interaction wired to that endpoint |
| Primary database | Running locally in a container, connected from your app, with one table created, written to, and queried |
| Async / background work | A job enqueued from your app and executed by a separate worker process, with the result visible |
| Auth | A user created and logged in end to end. Note honestly how painful the setup was versus a simpler option. |
| File / object storage | A file uploaded via SDK, retrieved, and downloaded |
| Any external API or model | Run your *hardest realistic* input through it. Compare at least two providers side by side on quality, latency and cost. |
| Containerisation | A Dockerfile built and running, behaving identically to local |
| Local orchestration | One compose file bringing up app + database + queue, all talking to each other, from a cold start |

### The two questions each spike must answer

1. **Does it do the job?** Not in the tutorial — with your actual data shape.
2. **What does it cost you?** Setup time, operational burden, money at scale, and how hard it is to leave later.

### Deliverable — end of Week 3

An **Architecture Decision Record (ADR)** per major choice, in `assets/adrs/`:

```
# ADR-00N: {{technology}}
Date: {{date}}
Status: accepted | superseded by ADR-00N

## Context
What problem forced this decision?

## Decision
What we chose.

## Alternatives considered
What else, and why not.

## Trade-offs accepted
What this costs us, honestly.

## Reversal cost
How hard is it to change this in 12 months? (cheap / moderate / effectively permanent)
```

That last field is the one people skip and later regret. A cheap-to-reverse decision deserves fifteen minutes; an effectively-permanent one deserves the whole spike.

---

## Week 4 — Accounts, Domain & Legal Groundwork

Boring, but these have lead times and block later work.

### Task checklist

| Task | Action | Typical cost |
|---|---|---|
| Domain | Buy `{{DOMAIN}}`. Check regional TLDs too. | ~$10/yr |
| Container registry | Org account under the product name. Create repos per service. | Free tier |
| Code host org | Create an **organisation**, not a personal account. Set up the repo structure. | Free tier |
| Hosting account | Sign up, add payment, provision the smallest instance, SSH in, install runtime, tear it down. Prove the loop works. | Cents |
| Product email | `hello@{{DOMAIN}}` via a mail provider or email routing. | Free tier |
| Data protection law | What does `{{JURISDICTION}}`'s law require for the data you handle? Note obligations that affect *architecture* (residency, deletion, consent, breach notice). | Research |
| Privacy policy draft | What you collect, why, how long you keep it, who can access it. Draft, don't publish. | Research |
| Terms of service draft | What users agree to, what you're not liable for. Draft, don't publish. | Research |
| Analytics decision | Pick one, document why, note what it means for your privacy policy. | Research |
| Licence decision | If open source: which licence, and what does it mean for contributions? Add `LICENSE` now. | Free |

**Why the legal research belongs in Week 4, not Week 11:** data residency, retention and deletion requirements are *architecture* constraints. Discovering them after the schema is designed means redesigning the schema.

### Deliverable — end of Week 4

All accounts created, domain bought, hosting loop tested and torn down, legal drafts started. Infrastructure scaffolding ready for Month 3.

---
---

# MONTH 2 — Architecture & System Design
### Weeks 5–8 | Design on paper so completely that coding becomes filling in blanks

Every artefact this month should let a new developer understand the system without asking you anything.

---

## Week 5 — Data Model Design

Your schema is the skeleton. Get it wrong and you write painful migrations for years.

### Table archetypes

Most products need some version of these. Name them for your domain.

| Archetype | Holds | Notes |
|---|---|---|
| **Identity** | users / accounts | id, email, name, role, status, created_at, last_seen_at |
| **Tenancy** | org / team / workspace | Add this in v1 even if v1 is single-user. Retrofitting multi-tenancy is one of the most expensive migrations there is. |
| **Grouping** | the container your users organise work into | |
| **Core entity** | the thing your product is fundamentally about | |
| **Artefact / upload** | files, with a storage key rather than the bytes | Store the object key, size, mime type, original filename |
| **Async job** | queued work | job id, external task id, status, error, started_at, finished_at |
| **Result / output** | what the user came for | Keep a structured JSON column for detail that will change shape |
| **Audit log** | who did what to what, when | actor, action, resource type, resource id, ip, user agent, timestamp |
| **Reputation / scoring** | derived standing, if your product has it | Decide now whether it's global or scoped per topic/context. Very hard to change later. |

### Questions to answer for every table

- **Primary key strategy** — UUID or sequential integer? UUIDs avoid enumeration attacks and let you generate ids client-side; integers are smaller and index better. Pick one and apply it everywhere.
- **Indexes** — every foreign key, and every column you'll filter or sort on. Write them into the diagram.
- **Soft vs hard delete** — anything about a real person is soft-delete only, with a documented purge path for deletion requests.
- **Nullability** — write `NOT NULL` explicitly on the diagram. Nullable-by-default is how you get bad data.
- **Retention** — how long do uploads live? Results? Logs? This is a legal answer as much as a technical one.
- **Denormalised / cached columns** — if you cache a count, write down what invalidates it.
- **Timestamps** — store everything in UTC. Decide now where you convert.

### Deliverable — end of Week 5

An **ERD** in draw.io or dbdiagram.io. Every table, column, relationship and cardinality. Export PNG + PDF to `assets/architecture/`. Both contributors sign off — this is the database contract for v1.

---

## Week 6 — API Contract Design

Design every endpoint before writing framework code. Once agreed, both contributors can work in parallel without blocking each other.

### Endpoint inventory

| Method + Path | Purpose | Auth |
|---|---|---|
| `POST /auth/login` | credentials → tokens | Public |
| `POST /auth/{{provider}}` | SSO callback → tokens | Public |
| `POST /auth/refresh` | refresh → new access token | Refresh token |
| `GET /me` | current user + tenancy | Required |
| `GET /{{resource}}` | list, paginated | Required |
| `POST /{{resource}}` | create | Required |
| `POST /{{resource}}/:id/{{bulk-action}}` | bulk import | Required |
| `POST /{{long-operation}}` | enqueue work → return job id | Required |
| `GET /jobs/:id` | poll job status | Required |
| `GET /{{output}}/:id` | fetch result | Required |
| `GET /{{output}}/:id/export` | download in a chosen format | Required |
| `DELETE /{{resource}}/:id` | delete resource and its artefacts | Required |

### Document five things per endpoint

1. **Request shape** — exact field names, types, required vs optional, size limits
2. **Success response** — exact JSON structure, every field, what it means
3. **Error responses** — every status code it can return, and the error body format (pick *one* error format for the whole API)
4. **Rate limiting** — per IP for public, per user for authenticated. Upload and model-calling endpoints definitely.
5. **Idempotency** — can this be safely retried? Anything that creates a resource or sends a message needs an answer here.

### Cross-cutting decisions to settle once

- Pagination style — offset or cursor? Cursor scales; offset is simpler. Pick one for the whole API.
- Versioning — `/v1/` in the path from day one, even if there'll never be a v2. It's free now and expensive later.
- Casing — `snake_case` or `camelCase` in JSON. One, everywhere.
- Timestamps — ISO 8601 UTC in every response.
- Long operations — always enqueue-and-poll, never block the request.

### Deliverable — end of Week 6

An **`openapi.yaml`** in `assets/api/`. Your framework may generate this later — writing it by hand first forces precision. Validate it renders in a Swagger editor.

---

## Week 7 — User Flows & Wireframes

Every screen the user will see. Not polished — structure, hierarchy, states.

### Screen inventory

- **Landing** — what does someone see before signing up? Value proposition in one sentence.
- **Sign up / log in** — including SSO if you're offering it
- **Onboarding** — first-run: what's the shortest path to the user's first real result?
- **Home / dashboard** — recent activity, overview, quick actions
- **Primary workflow** — the multi-step flow that is the reason your product exists. Wireframe every step.
- **In-progress / job status** — what's shown while work runs. Progress? Estimate? Can they leave and come back?
- **Result view** — the payoff screen
- **Export / share**
- **Management screens** — the lists and tables where users organise things
- **Settings** — profile, credentials, notification preferences
- **Notification preferences** — if you send anything, users need granular control and a one-click unsubscribe. This is a legal requirement in most jurisdictions, not a nicety.

### The states everyone forgets

Wireframe these explicitly for every screen above:

| State | Question |
|---|---|
| **Empty** | Brand-new account, nothing exists yet. What's here? |
| **Loading** | What's on screen while data fetches? |
| **Partial** | Some data loaded, some failed. |
| **Error** | Operation failed. What can the user actually *do* about it? |
| **Permission denied** | User can't access this. |
| **Too much data** | 10,000 rows. Does the page survive? |

### Annotate every wireframe with

- What data is shown and which endpoint supplies it
- What happens on every click
- What the loading and error states look like
- What's above the fold on the smallest screen you support

### Deliverable — end of Week 7

A wireframe per screen, exported to `assets/wireframes/`. Share the editable link so both contributors can comment. Do not start Month 3 until both are happy with every screen.

---

## Week 8 — System Architecture Diagrams

Three diagrams that both contributors can explain from memory.

### Diagram 1 — Deployment architecture

- Every service and container, labelled with image and port
- Which are internet-facing (ideally exactly one reverse proxy) and which are internal-only
- How the proxy routes traffic across hosts and paths
- The internal network — who can talk to whom
- Volumes — which service writes where, and what lives there
- External calls — which service calls which third party, and on what trigger
- Where backups go and how you'd restore from them

### Diagram 2 — Request lifecycle (happy path, end to end)

Trace one complete run of `{{CORE_JOB}}` from click to result. A useful shape:

1. User submits input from the client
2. Client calls the API
3. API validates, stores artefacts, writes records
4. API enqueues async work and immediately returns a job id
5. Client polls (or subscribes) for status
6. Worker picks up the job, does the work, writes results
7. Worker updates job status and emits a completion event
8. Notification is delivered on the user's chosen channel
9. Client shows the result; user exports it

**Then draw the unhappy paths.** Each of these is a real branch you'll have to write code for:

- The worker crashes mid-job
- The external API times out or rate-limits you
- The user closes the tab and returns tomorrow
- The same request arrives twice
- The job succeeds but the notification fails

### Diagram 3 — Security & data boundary map

- What's encrypted in transit? (everything — enforce HTTPS at the proxy)
- What's encrypted at rest? Document the actual config, not the intent.
- Which ports are open on the host firewall? Justify each one.
- Where do secrets live — and confirm none are in version control
- What happens to uploaded artefacts after processing? State the retention policy.
- How are tokens validated, by which service, and what happens on expiry?
- **Map every field that identifies a real person.** Where it enters, where it's stored, where it leaves, who can read it. This map is the thing you'll need if anyone ever asks a compliance question.

### Deliverable — end of Week 8

All three diagrams in `assets/architecture/`, exported as PNG.

These are **living documents**. Add a line to `CONTRIBUTING.md`: any PR that changes the architecture updates the diagram in the same PR. An out-of-date diagram is worse than none.

---
---

# MONTH 3 — Specifications, Standards & Launch Prep
### Weeks 9–12 | Write the rules you'll code by, so Month 4 has zero ambiguity

---

## Week 9 — Feature Specifications

A spec answers *exactly* how a feature behaves — the precise rules, edge cases and failure modes. One document per major feature area. Plain English, no code.

### Spec template

```
# Spec: {{feature}}

## Purpose
One sentence. What does this do for the user?

## Inputs
Every accepted input, its format, size limits, and what happens when limits are exceeded.

## Rules
The exact logic. Thresholds, defaults, who can override what.

## Edge cases
Empty input. Malformed input. Duplicate submission. Concurrent submission.
Partial success. Maximum scale.

## Failure modes
For each way this can fail: what the user sees, what's logged,
whether it retries, and whether it's recoverable.

## Configuration
What's a system default, what's user-configurable, and who can change it.

## Acceptance criteria
Numbered, testable statements. These become your tests in Month 4.
```

### Question bank — run each feature through these

**Rules and thresholds**

- What are the exact numeric thresholds, and who chose them?
- Are they fixed, admin-configurable, or per-user?
- How are results rounded or bucketed, and who decided?
- Is partial credit / partial success a thing? What does it mean precisely?

**Inputs and limits**

- Which formats are accepted? What's the maximum size, per item and per batch?
- What's the maximum batch size, and what happens at the limit?
- What's the minimum quality of input below which you refuse rather than guess?

**Ambiguity**

- When the system isn't confident, does it fail loudly, guess, or flag for human review? Pick one per feature and be consistent.
- Can a user re-run an operation with different settings? What happens to the previous result — replaced, versioned, or kept side by side?
- Does the output show its evidence, or just its conclusion?

**Async work** (spec this once, globally)

- How many concurrent workers can your target host actually run?
- What's the timeout for a single job, and what happens when it's exceeded?
- How many retries, with what backoff?
- How does the user learn a job finished — which channels, and can they turn each one off?
- How long are job logs and error messages retained?
- **How do you stop one user's huge batch from starving everyone else's small job?** Per-user queues, fair scheduling, or concurrency caps — decide now.
- If a worker dies mid-job: resume, restart, or fail?

**Notifications** (if you send anything)

- What's the trigger, and what's the delivery channel?
- What's the batching or digest rule that prevents notification fatigue?
- What's the per-user daily cap?
- How does someone unsubscribe, and does that survive across channels?
- Is delivery idempotent — can a retry send the same message twice?

### Deliverable — end of Week 9

One spec per feature in `assets/specs/`.

> If you can't write the spec, you don't understand the feature well enough to build it.

---

## Week 10 — Coding Standards & Repository Structure

Agree how you write code before you write any. Two developers with different habits produce a codebase that reads like two products stitched together.

### Standards table

| Standard | The rule | Tooling |
|---|---|---|
| Formatting | One auto-formatter per language, default config, runs on save. No manual formatting debates, ever. | {{tool}} |
| Linting | One linter per language. Warnings are errors in CI. | {{tool}} |
| Type checking | If your language has it, turn it on now. Retrofitting types is miserable. | {{tool}} |
| Branch strategy | `main` = production. `dev` = integration. `feature/short-name` = individual work. | Branch protection |
| Pull requests | No self-merging. Every PR reviewed by the other contributor. | Branch protection |
| Commit messages | Conventional Commits: `feat:` `fix:` `docs:` `refactor:` `test:` `chore:` | commitlint in CI |
| Environment config | `.env.example` in the repo with placeholders and a comment per variable. Real `.env` never committed. | Create it now |
| Secrets | CI secrets for pipelines, env file on the host, zero hardcoded credentials. | Audit before every deploy |
| Error handling | No silently swallowed exceptions. Every caught error is logged with context. Structured logging from day one. | Enforced in review |
| Logging | Structured (JSON) logs with a request id threaded through. Decide now what must never appear in a log. | {{tool}} |
| Testing baseline | Every new function gets a unit test. Every endpoint gets an integration test. Every spec acceptance criterion maps to a test. | {{tool}} |
| Naming | One convention per language, enforced by the linter, not by review comments. | Linter |
| Dependencies | Lockfiles committed. Automated vulnerability alerts on. A named day each month for updates. | Dependabot / Renovate |

### Repository structure

```
{{product}}/
├── {{service-a}}/
│   ├── Dockerfile
│   └── src/
├── {{service-b}}/
│   ├── Dockerfile
│   └── src/
├── assets/
│   ├── adrs/              ← Architecture Decision Records
│   ├── specs/             ← Feature specifications
│   ├── architecture/      ← System diagrams + ERD
│   ├── wireframes/        ← UI wireframes
│   └── api/               ← openapi.yaml
├── .github/workflows/
├── docker-compose.yml         ← local development
├── docker-compose.prod.yml    ← production
├── .env.example
├── .gitignore
├── LICENSE
├── README.md
└── CONTRIBUTING.md
```

### Deliverable — end of Week 10

A **`CONTRIBUTING.md`** at the repo root containing every standard above, written for someone who has never seen this project. This is your onboarding doc for the next contributor — including future-you in eight months.

---

## Week 11 — Security & Compliance Checklist

Security isn't a feature you add at the end. These are decisions you build around from day one.

### Decisions to make, document and build around

**Authentication & sessions**

- Token lifetimes — short-lived access, longer refresh. Document the rotation logic.
- What happens on logout? On password change? Are existing sessions revoked?
- Password policy — length minimum, and a check against a known-breach corpus at sign-up.
- Is MFA in scope for v1? If not, when?

**Input handling**

- Validate file type by content, server-side — never trust the extension.
- Define an allowlist of accepted types. Allowlist, not blocklist.
- Size limits enforced *before* the file lands on disk.
- Rate limiting: per IP for public endpoints, per user for authenticated ones.
- CORS: explicit origin list. Never a wildcard in production.
- Injection: use the ORM/parameterised queries. Ban raw query string construction in `CONTRIBUTING.md`.
- **Authorisation on every single object access.** Check that this user owns this resource, on every read as well as every write. Missing object-level checks is the most common serious bug in products at this stage.

**Data**

- Classify what's personal data in your system, field by field.
- Document the handling rule per field: who can read it, is it logged, is it exported, when is it purged.
- Data residency — where does your host physically store it, and is that acceptable under `{{JURISDICTION}}`?
- Deletion — when a user asks to be deleted, what exactly happens, and how long does it take?
- Backups — encrypted, tested restore, and covered by the same retention rules as live data.

**Operations**

- Temp files: random names, isolated directory, deleted immediately after use.
- Automated dependency vulnerability alerts, enabled on every package ecosystem you use.
- Audit logging on every create, update and delete touching personal data.
- What's your response if you're breached? Two paragraphs is enough. Having none is the problem.

### Deliverable — end of Week 11

A checklist at `assets/security.md`, with columns for **verified by** and **verified on**. Every item ticked before launch. Treat it as a pre-flight checklist — non-negotiable.

---

## Week 12 — Soft Launch Preparation

Remove every possible friction from Month 4. When you sit down to write production code, there should be zero setup left.

### Final checklist

| Task | Detail | Owner |
|---|---|---|
| Repo skeleton | All folders, `README.md`, `CONTRIBUTING.md`, `LICENSE`, `.env.example`, `.gitignore` | Both |
| Compose skeleton | Local and production compose files with every service defined, even if images don't exist yet | Both |
| CI/CD skeleton | Build → test → push → deploy workflow. Wire it up now, even against a stub. | Both |
| Project board | Kanban with an issue per v1 feature. Labels per area. Every issue has an owner and done criteria. | Both |
| Migrations setup | Choose the migration tool. Generate the initial migration from your ERD. Don't run it yet. | {{owner}} |
| Staging plan | Separate host, or a second stack locally? Document the exact promotion flow. | Both |
| Observability plan | Where do logs go? What's your uptime check? What alerts you at 3am, and what doesn't? | Both |
| Soft launch criteria | Write down exactly what "ready to show real users" means. Five specific things that must work. | Both |
| Rollback procedure | The exact command sequence to revert to the previous release. Write it. **Test it.** | Both |
| Feedback plan | How do pilot users reach you? Set the channel up now, not on launch day. | Both |
| First sprint | Month 4 Week 1: exact tickets for each contributor, with done criteria. | Both |

### Deliverable — end of Week 12

The repo exists with a full skeleton. Every planning artefact is in `assets/`. The board has every v1 feature as a ticket with an owner and done criteria. Both contributors know exactly what they're building on Day 1 of Month 4.

**Planning phase complete.**

---
---

# Appendix

## 12-week summary

| Week | Theme | Deliverable | Both contributors agree on |
|---|---|---|---|
| W1 | User research | Persona document | Who we're building for |
| W2 | Scope | Signed-off v1 feature list | What's in and what's out |
| W3 | Stack validation | ADRs | Every tech choice and why |
| W4 | Groundwork | Accounts, domain, legal drafts | Infrastructure scaffolding |
| W5 | Data model | ERD | Every table and relationship |
| W6 | API contract | `openapi.yaml` | Every endpoint shape |
| W7 | Wireframes | All screens and states | Every screen and flow |
| W8 | Architecture | 3 system diagrams | Deployment, lifecycle, security |
| W9 | Specifications | Spec per feature | Exact rules and edge cases |
| W10 | Standards | `CONTRIBUTING.md` | How we write and review code |
| W11 | Security | Security checklist | What we protect and how |
| W12 | Launch prep | Repo skeleton + board | Day 1 of Month 4 is unambiguous |

## Tool categories

Fill in your picks. Prefer free and open source where it doesn't cost you time.

| Need | Options to consider | Your pick |
|---|---|---|
| Diagramming | draw.io / diagrams.net, Mermaid (lives in git, diffs cleanly) | |
| Wireframing | Excalidraw, Figma free tier, Penpot | |
| ERD | dbdiagram.io, draw.io | |
| API spec | Swagger Editor, Stoplight | |
| API client | Bruno, Hoppscotch, Insomnia | |
| DB browser | TablePlus, DBeaver, pgAdmin | |
| Local orchestration | Docker Compose | |
| Analytics | Plausible, Umami, Matomo | |
| Uptime monitoring | Uptime Kuma (self-hosted), BetterStack free tier | |

## Reading, scheduled

| Read | When |
|---|---|
| Docs for each stack component — the full tutorial, not the quickstart | W3 |
| Your ORM's relationship and lazy-loading semantics | W5 |
| REST / API design guidelines of your choice | W6 |
| OWASP Top 10 — all ten, know what each means | W11 |
| Conventional Commits spec — five minutes, used forever | W10 |
| `{{JURISDICTION}}` data protection law, plain-English explainer | W4 |
| The Twelve-Factor App | W4 |

## Anti-patterns this roadmap exists to prevent

| Anti-pattern | Which week catches it |
|---|---|
| Building for a user you've never spoken to | W1 |
| v1 that never ships because it keeps growing | W2 |
| Choosing a stack from a blog post, not a spike | W3 |
| Discovering a legal constraint after the schema is fixed | W4 |
| Retrofitting multi-tenancy | W5 |
| Frontend and backend blocking each other for weeks | W6 |
| Shipping without empty, loading or error states | W7 |
| Nobody knowing how a request actually flows | W8 |
| Arguing about behaviour during code review | W9 |
| A codebase that reads like two different products | W10 |
| Finding an authorisation hole after launch | W11 |
| Day 1 of Month 4 spent on setup instead of code | W12 |
