# Project Guide: Journal Entry Testing for an Audit Engagement

## The scenario
You're a data analyst on an audit team. Auditing standards (ISA 240) require testing journal entries
because management can override controls by posting unusual entries. Instead of sampling a few entries,
you will analyse the **entire population** and decide which entries deserve the audit team's attention.

Your deliverables: a clean database, a set of repeatable tests, a Power BI dashboard, and a short
findings memo, all in a GitHub repo.

**Goal:** *Which journal entries look unusual or risky, why, and how confident are you?*

---

## Phase 0 - Set up (30 min)
- [ ] Create a GitHub repo (e.g. `audit-journal-entry-analytics`) with folders: `data/`, `sql/`, `python/`, `powerbi/`, `docs/`
- [ ] Install Python (3.10+), pandas, matplotlib, Jupyter; a SQL engine (SQLite is easiest; PostgreSQL or SQL Server Express to stretch); Power BI Desktop
- [ ] Add a `.gitignore` and write a first README with the business question
- [ ] Commit early and often with meaningful messages

## Phase 1 - Understand and profile the data (Python)
Audit analytics always starts with data validation. Before any analysis, prove the data is complete and reliable.
- [ ] Load all CSVs into pandas. Check row counts, data types, nulls, duplicates
- [ ] Check date ranges and that dates are real dates
- [ ] Does every journal balance (debits = credits)? Does every line have a header?
- [ ] Do all user_ids and account_ids exist in their reference tables?
- [ ] Build a **summary**: entries by source, by month, by user; manual vs system share
- [ ] Draw 3-4 charts that describe "normal" behaviour (volume by month, amount distribution, entries by weekday)
- [ ] Write down what "normal" looks like. You can't spot abnormal without it.

**Skills:** pandas, merging, groupby, data quality checks, Jupyter notebooks.

## Phase 2 - Build the database (SQL)
- [ ] Design a schema with primary and foreign keys, and sensible data types
- [ ] Load the CSVs into your database
- [ ] Create a view with one row per journal and its total debit and credit
- [ ] Practise, in increasing difficulty: filters, joins across 3+ tables, GROUP BY / HAVING, CASE, CTEs, window functions (COUNT/SUM/ROW_NUMBER OVER)
- [ ] Reproduce your Phase 1 summaries in SQL and confirm the numbers match

**Skills:** DDL, joins, aggregation, CTEs, window functions, date functions (they differ by SQL dialect, so look this up).

## Phase 3 - Audit tests (SQL + Python)
Auditors design **tests** that select entries with higher-risk characteristics. For each area below,
decide *what to look for, which columns to use, and what threshold is reasonable*. Choosing and
justifying thresholds is part of the skill.

| Area | Question to answer |
|---|---|
| Timing | Are manual entries being posted when the business is normally closed? |
| Amounts | Do any manual entries look like estimates or plugs rather than real transactions? |
| Authorisation | Are entries sitting just under an approval limit? Are any entries approved by the wrong person, or no one? |
| Segregation of duties | Can the same person create and approve? |
| User access | Did anyone post after they should have lost access? |
| Period-end / cut-off | What manual activity happens around year-end, and what happens to it afterwards? |
| Duplicates | Is anything recorded twice for the same vendor and document? |
| Late entry | Are entries keyed long after their posting date? |
| Unusual accounts | Are any postings going to an account that should normally be empty or cleared? |
| Narrative | Are there manual entries with no meaningful explanation? |
| Digit analysis | Do the leading digits of amounts follow Benford's Law? Research it, and the MAD measure, not just chi-square |
| Integrity | Anything that shouldn't be possible (e.g. unbalanced entries)? |

For every test, record in a test catalogue: **objective, logic, threshold and rationale, number of exceptions, and a false-positive assessment.**

**Hints**
- Some tests need only one table; others need joins to users, accounts, or other entries.
- A test returning zero rows is a valid and useful result.
- A good test has a low enough false-positive rate that a human could review every hit.
- Try writing each test in SQL first, then re-implement two in pandas to compare.

## Phase 4 - Risk scoring
- [ ] Combine your tests into a score per journal (weight tests by seriousness; justify your weights)
- [ ] Rank the top 20 journals. For each, write one sentence a manager would understand
- [ ] Look for journals flagged by multiple tests. Why are those the most interesting?
- [ ] Optional: try an unsupervised outlier method (z-scores, Isolation Forest) and compare it with your rules

## Phase 5 - Power BI dashboard
- [ ] Import your cleaned tables or exports; build a star schema (a date table, dimensions, facts)
- [ ] Create relationships. Note which one needs to be inactive and why
- [ ] Write DAX measures: totals, % manual, flagged entries, flagged value, exceptions by test
- [ ] Pages: (1) executive summary, (2) test drill-down, (3) people and approvals, (4) timing, (5) Benford
- [ ] Add slicers, a drill-through page for a single journal, and tooltips
- [ ] Apply a consistent, readable design (one accent colour for "risk")
- [ ] Screenshot every page into `docs/images/`

**Skills:** data modelling, DAX (CALCULATE, DIVIDE, USERELATIONSHIP), visual design, storytelling.

## Phase 6 - Communicate like an auditor
- [ ] Write a 1-2 page **findings memo**: scope, approach, results by test, top risks, recommendations, limitations
- [ ] Recommend follow-up procedures. What would you ask management or inspect next?
- [ ] State limitations honestly (synthetic data, thresholds chosen, false positives)
- [ ] Finalise the README: problem, data, method, results, screenshots, how to run it

## Phase 7 - Check your work (do this LAST)
Only after finishing your own analysis, open `_check_last/anomaly_labels.csv`. It lists, per journal, the
issue that was deliberately planted. Compare against your results:
- Which did you find? Which did you miss? Why?
- Which of your flags were not planted? Were they real false positives or a sign your threshold was off?
- Calculate precision and recall per test.

Real engagements have no answer key, so use it to learn rather than to score yourself.

---

## Stretch goals
- Parameterise thresholds in a config file and re-run
- Add automated tests (pytest) and a GitHub Actions workflow
- Port the SQL to a second dialect
- Monetary unit sampling: draw an audit sample from the population and justify the size
- Account-pair analysis (unusual debit/credit combinations)
- Near-duplicate detection (fuzzy matching on references and amounts)

## What you'll be able to say afterwards
"I analysed 100% of a general ledger using SQL and Python, built a risk-scoring model, and delivered
a Power BI dashboard and audit memo." That is exactly the work audit data analytics teams do.

## Rules for the repo
- Only synthetic or public data in a public repo. Never real client data.
- Don't use any firm's logo or name as if the project were official.
