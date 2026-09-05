# sme

A public Q&A platform where people self-assign topic roles, questions route to the people holding that role, and answering builds reputation scoped to that topic.

Positioned as the escalation path for AI failure — people come here when the AI answered badly and nothing existing fits.

> **Status:** Pre-development planning · **Contributors:** [@sho6000](https://github.com/sho6000) · [@YojitShinde](https://github.com/YojitShinde) · [@amaljyothis2003](https://github.com/amaljyothis2003)
> Living document. Modify anytime.

---

## 12-Week Plan

One deliverable per week, committed to this repo. A week without its artefact didn't happen.

### Month 1 — Research & Decisions

**Week 1 — Know your user**
Survey developers and scan the competitive landscape. Write two personas: the Answerer (design for this one — supply is what kills marketplaces) and the Asker.
→ `assets/research/personas.md`

**Week 2 — Scope**
Lock the v1 feature list. Two calls gate everything: threaded Q&A or live chat, and public community or self-hostable.
→ `assets/scope/v1.md`, both names on it

**Week 3 — Stack validation**
Half a day per spike, both of us, hands on. Include email deliverability — the whole product is an email that has to arrive.
→ one ADR per choice in `assets/adrs/`

**Week 4 — Groundwork**
Domain, GitHub org, hosting loop, licence, DPDP research, privacy and ToS drafts.
→ accounts live, drafts started

### Month 2 — Design

**Week 5 — Data model**
Tables, columns, relationships. Reputation per-topic, never global. Single Postgres.
→ ERD in `assets/architecture/`

**Week 6 — API contract**
Every endpoint, request and response shape, error format, rate limits.
→ `assets/api/openapi.yaml`

**Week 7 — Wireframes**
Every screen, plus empty / loading / error states. Start with the expert inbox.
→ `assets/wireframes/`

**Week 8 — Architecture**
Deployment, request lifecycle, security boundaries. Plus the routing service — the only part nobody else has built.
→ three diagrams in `assets/architecture/`

### Month 3 — Specs & Prep

**Week 9 — Specifications**
Role granting, routing behaviour, notifications, reputation. Plain English, no code.
→ `assets/specs/`

**Week 10 — Standards**
Formatting, linting, branches, commits, tests, repo structure.
→ `CONTRIBUTING.md`

**Week 11 — Security**
Object-level authorisation, rate limits, unsubscribe, audit trails.
→ `assets/security.md` with verified-by columns

**Week 12 — Launch prep**
Repo skeleton, CI/CD, board, rollback procedure, first sprint tickets.
→ Day 1 of build is unambiguous

---

## Open decisions

| # | Decision | Blocks |
|---|---|---|
| 1 | Threaded Q&A or live chat? | Weeks 5–9 |
| 2 | Public community or self-hostable instances? | Data model, identity |
| 3 | How is a topic role granted — and lost? | Week 9 |
| 4 | Fan-out policy: broadcast, round-robin, or ranked? | Routing service |
| 5 | Notification channel — email alone can't do "minutes" | Week 8 |

---

## Repo structure

```
sme/
├── api/  web/  worker/
├── assets/
│   ├── research/  scope/  adrs/  specs/
│   ├── architecture/  wireframes/  api/
│   └── security.md
├── docker-compose.yml
├── LICENSE  README.md  CONTRIBUTING.md
```
