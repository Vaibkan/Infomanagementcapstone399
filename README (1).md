# SIGNAL — Student Engagement & Early-Support Platform

SIGNAL is an advisor-facing web app that helps **Student Support Advisors** spot students who may benefit from early academic or wellbeing support, see exactly *why* each student was flagged, and record their own decisions.

It was built as part of a **University of Auckland INFOMGMT 399 capstone project**.

> ⚠️ **Prototype only.** Every student record is fictional and synthetic (one demo course, `BUSAN101 2026S2`, 100 students). SIGNAL is not connected to Canvas, does not contact students, and must not be used with real student data.

---

## What it does

- **Transparent rule engine**: no machine learning, no dropout scores, no hidden classifications. Each rule returns **Matched**, **Not matched**, or **Could not check**.
- **Review queue**: a student enters the queue when at least one enabled rule matches.
- **Human in control**: advisors make every decision. Nothing is automated.
- **Missing data handled honestly**: a blank value is never treated as zero activity or a missed assessment.
- **Fairness by design**: group attributes and student names are never used in rule calculations. They appear only in a separate fairness diagnostic page.

### The four rules

| Rule | Name | Triggers when… | Suggested support |
|------|------|----------------|-------------------|
| **R1** | Missed early assessments | 2+ of 3 early assessments (Quiz 1, Quiz 2, Assignment 1 plan) are confirmed *Missed* | Academic skills support |
| **R2** | Canvas activity dropped | Weeks 3 & 4 are ≤ 50% of a Weeks 1–2 baseline of at least 4 sessions | Wellbeing check-in |
| **R3** | Late registration with low activity | Registered after semester start, and Weeks 1 & 2 each had fewer than 3 sessions | Enrolment & orientation support |
| **R4** | No activity for two weeks | Zero Canvas sessions in any two consecutive weeks | Wellbeing check-in |

When several rules match, the suggested category follows a fixed priority: **R1 → R4/R2 → R3**. All thresholds can be changed on the **Rule Settings** page.

---

## Pages

| Page | Route | Purpose |
|------|-------|---------|
| Ask SIGNAL | `/` | Answers data-grounded questions about the cohort (never invents data) |
| Review Queue | `/review` | Students with one or more matched rules |
| Student Explorer | `/explorer` | Browse and filter all students |
| Student Profile | `/student/:id` | Per-student signals, rule explanations, and decision recording |
| Data Quality | `/data-quality` | Missing or unrecorded engagement fields |
| My Decisions | `/decisions` | Decisions made this session, with CSV export |
| Fairness Review | `/fairness` | Review rates compared across student groups (diagnostic only) |
| Rule Settings | `/rule-settings` | Turn rules on or off and adjust thresholds |
| About | `/about` | Method, privacy principles, and limitations |

---

## Tech stack

- **React 18** + **Vite**
- **Tailwind CSS** + **shadcn/ui** (Radix UI) components
- **React Router**, **TanStack Query**, **Recharts**, **Framer Motion**
- **Base44** for the backend, auth, and data entities (`Student`, `User`)

## Project structure

```
├── base44/
│   ├── config.jsonc          # Base44 app config
│   └── entities/             # Data schemas (Student, User)
├── public/
├── src/
│   ├── api/base44Client.js   # Base44 SDK client
│   ├── components/
│   │   ├── ui/               # shadcn/ui primitives
│   │   └── ui-signal/        # SIGNAL-specific components
│   ├── lib/
│   │   ├── ruleEngine.js     # The four transparent rules
│   │   ├── assistant.js      # Ask SIGNAL question answering
│   │   ├── signalContext.jsx # Shared app state
│   │   └── csvExport.js      # Decision export
│   ├── pages/                # One file per route
│   └── App.jsx               # Routes
├── package.json
└── vite.config.js
```

---

## Getting started

### Prerequisites

- [Node.js](https://nodejs.org/) 18+
- The Base44 CLI: `npm install -g base44@latest`
- [Deno](https://docs.deno.com/runtime/getting_started/installation/) (the local Base44 backend runs on it)

### Run locally

```bash
git clone https://github.com/<your-username>/signal-support-flow.git
cd signal-support-flow
npm install

base44 login   # once per machine
base44 link    # once per clone; links this folder to your Base44 app
base44 dev     # starts the local backend and the frontend together
```

Open the URL that `base44 dev` prints (usually `http://localhost:5173`).

**Notes**

- Use `base44 dev`, not `npm run dev`. On its own, `npm run dev` serves the UI with no backend, so every `/api` call fails.
- `base44 link` writes `base44/.app.jsonc`, which is git-ignored on purpose. Every new clone needs to run it.
- The app must be published at least once on Base44 before the UI loads under `base44 dev`.
- Local entity data is held in memory and is wiped when `base44 dev` restarts.
- To work on the frontend against the live hosted backend, run `base44 dev --remote`. Be careful: in that mode, writes go to production data.

### Other scripts

```bash
npm run build      # production build to ./dist
npm run lint       # ESLint
npm run preview    # preview the production build
```

---

## Privacy & responsible use

- **Purpose limitation**: the data is used only to support advisor review.
- **Data minimisation**: the app keeps only the engagement signals the rules need.
- **No automated decisions**: the app never contacts students or triggers interventions.
- **Session-only decisions**: advisor decisions are held in temporary session memory.
- Any real deployment would need to address the **NZ Privacy Act 2020**. This prototype does not guarantee legal compliance.

## Limitations

- One fictional course and 100 synthetic students.
- Data covers only Weeks 1–4, with no outcomes after that checkpoint.
- The fairness comparisons use small fictional groups and do **not** show whether the rules are fair.
- The assistant answers only supported, data-grounded questions.

---

## Acknowledgements

Developed for INFOMGMT 399 (Capstone), University of Auckland. Scaffolded with [Base44](https://base44.com).
