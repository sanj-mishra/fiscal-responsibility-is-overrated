# fiscal-responsibility-is-overrated — Roadmap

A private, **local-first** finance app.

PDF statements in → **Ollama** categorizes → **Streamlit** dashboard → a file-based
memory that grows into an advisor learning your goals. Nothing leaves your machine.

This file is the plan, not the code. Build is sequenced so each phase is useful on its own.

## Chosen defaults

- **Input:** PDF statements you drop in `data/inbox/` (no stored credentials).
- **PDF parsing:** hybrid — `pdfplumber` extracts text/tables first (fast, exact
  numbers); fall back to Ollama only when a layout won't parse cleanly. Reconcile
  parsed transactions against the statement's stated total before saving.
- **Stack:** Python + Streamlit.
- **Storage:** SQLite for transactions; JSON/Markdown files for the agent's memory.
- **Model:** start with `llama3.2`; pull a larger model for the advice phase.

## Phase 1 — Ingest + Dashboard

- [ ] SQLite schema for normalized transactions, with a dedup hash (re-import is safe).
- [ ] PDF ingest (hybrid): `pdfplumber` text/table extraction → Ollama fallback for
      messy layouts → normalize (date, amount, merchant, account). Handle sign
      conventions and date formats.
- [ ] Validation: reconcile parsed totals against the statement; flag mismatches
      instead of silently importing bad numbers.
- [ ] Categorize: rules-cache first (`merchant → category`), Ollama only for new merchants.
- [ ] Streamlit dashboard: spend by category, top merchants, month-over-month trend.

## Phase 2 — Goals & Memory

- [ ] `goals.md` (free text) + `preferences.json` (budgets, rules, feedback log).
- [ ] UI to edit goals and set per-category budgets.
- [ ] Budget-vs-actual on the dashboard.

## Phase 3 — Advisor Agent

- [ ] Build a spending summary + inject goals/preferences into the prompt.
- [ ] Suggestions: where to cut, where there's room to spend, notable trend changes.
- [ ] 👍/👎 on each suggestion, logged to memory so future advice adapts.
- [ ] Switch to a larger Ollama model for better reasoning.

## Phase 4 — Patterns (later)

- [ ] Subscription / recurring-charge detection.
- [ ] Anomaly flags ("dining up 40% vs your 3-month average").
- [ ] Optional alternate loaders: CSV exports, Plaid.

## Safety

- All of `data/` is gitignored — transactions, db, and memory never get committed.
- Ingest is idempotent (hash dedup) so re-running never double-counts.
- Parameterized SQL; LLM output parsed defensively with a safe fallback.

## Open questions

- Which bank/card PDF statements to support first? (need one sample to build against)
- Pull a larger Ollama model now, or wait until Phase 3?
