# Statvane

A research website: collect **primary data** with surveys, gather **secondary data** from public sources and the
web, then download it or analyse it, with results you can take into **SPSS, R/RStudio, Stata, jamovi, Python or
Excel**. Accounts, teams and paid plans (Stripe) included.

**Quickest test:** open this repository in GitHub Codespaces (Code → Codespaces → Create codespace). It installs and
starts itself; see [CODESPACES.md](CODESPACES.md).

## What it does

**Public website (server-rendered, search-engine ready)**

- Home, Services, Tools, Pricing, Blog, About and Contact, with a footer linking Terms and conditions, Privacy,
  Cookies, Refunds and Acceptable use.
- Every page has its own title and description, canonical link, Open Graph tags and structured data. There is
  also a sitemap and robots.txt.
- The blog and contact messages are managed by administrators inside the app.

**Accounts and teams**

- Sign up and log in, password reset by email, and a personal dashboard.
- Projects with owner, editor and viewer roles and link invitations.
- Activity log, archive, and a whole-project download (.zip).

**Primary data: surveys**

- Builder with sections, display logic, matrix and ranking questions, file uploads and consent.
- Publish with a link or QR code; the public form is designed for phones.
- Completion monitoring. Responses become a labelled dataset, or download as Excel, CSV, SPSS, R or jamovi.

**Secondary data: public sources and the web**

- Built-in sources: World Bank (World Development Indicators), IMF, UN SDG database, WHO Global Health
  Observatory, Eurostat, FRED (needs a free key), SEC EDGAR company financials and Yahoo Finance prices.
- Tables from **any web address**: HTML tables, CSV, Excel, JSON and tables in PDF reports.
- An **AI data-search agent** that searches the built-in sources and the wider web for the data you describe. It
  only suggests addresses found in real search results, and nothing is imported until you choose it.
- Every dataset records its source, retrieval date and citation. Download it as Excel or analyse it like any
  other dataset.

**Data preparation**

- Import CSV/TSV, Excel, SPSS (.sav/.zsav), Stata (.dta), R (.rds/.RData) and jamovi (.omv), keeping labels,
  measures and missing codes.
- An immutable change history: recode, compute, reverse-code, relabel, missing codes, cell edits. Compare
  versions and restore any of them.
- Quality checks for likely missing-value codes, duplicates and outliers.

**Statistics.** 18 validated procedures:

- descriptives and frequencies;
- crosstabs;
- t-tests and ANOVA;
- Mann–Whitney, Wilcoxon, Kruskal–Wallis and χ² goodness of fit;
- correlation;
- linear and logistic regression;
- Cronbach's α;
- PCA, factor analysis and clustering.

Each procedure reports effect sizes, assumption checks and plain-language warnings.

**Your tool.** Every analysis can be run again in a real tool, with the output files kept:

- **Python** (statsmodels/SciPy);
- **R** (the engine RStudio runs);
- **jamovi** (its jmv engine);
- **SPSS syntax** in GNU PSPP (.spv output);
- **Excel**, as a workbook with live formulas.

It also gives Stata `.do` and SPSS `.sps` syntax, plus the data in each tool's format. Stata and IBM SPSS
Statistics are commercial programs and aren't run on the server.

**Results and reports**

- Typeset tables, interactive charts, a chart builder, dashboards and research notes.
- Word, PDF or HTML reports with chosen sections and order. AI text is labelled, and no reference is ever invented.

**AI (optional)**

- Interpretations with every number traced to the results.
- A project assistant whose proposals are validated and applied only when you confirm.
- It works from metadata and results only, never your data rows.

**Plans and payments**

- Free, Professional and Institution plans with monthly or yearly billing through **Stripe Checkout**.
- Payment methods are chosen in the Stripe Dashboard: cards, plus PayPal, Apple Pay and Google Pay where Stripe
  offers them.
- Customers use Stripe's customer portal to change cards, get invoices and cancel.
- Webhooks are verified by signature and are idempotent. Administrators can also set plans by hand.

## Try it locally

Requirements: Python 3.12 or newer (developed on 3.13). No database server is needed.

```bash
cd backend
pip install -r requirements.txt
python -m risp.app                 # http://127.0.0.1:8000
```

Create an account and a project, then choose **Add the synthetic demo dataset** to try the workflow on clearly
labelled fictional data. Running analyses in R, jamovi and PSPP needs those programs; `deploy/install-tools.sh`
installs them on Ubuntu. The Docker image already includes them.

## Put it online

See **[docs/DEPLOYMENT.md](docs/DEPLOYMENT.md)**. It covers:

- Docker Compose, or a one-command Ubuntu/Debian install;
- automatic HTTPS;
- backups with tested restore;
- health checks;
- **setting up Stripe step by step**;
- hosting recommendations.

All settings are explained in [deploy/env.example](deploy/env.example).

## Tests

```bash
cd backend
python -m unittest discover -s tests -t .        # statistics, file formats, API, billing, security, operations
pip install -r requirements-dev.txt && python -m playwright install chromium
python tests/ui/walkthrough.py http://127.0.0.1:8000 /tmp/shots   # browser walkthroughs (server running)
python tests/ui/billing.py /tmp/shots                               # buying a plan, against a fake Stripe
```

GitHub Actions (`.github/workflows`) does four more things this repository's development environment can't:

- it runs the unit suite;
- it runs every generated script in real R, jamovi, PSPP and Python and compares the key numbers;
- it fetches from every live data source;
- it builds and smoke-tests the Docker image.

Logs are saved to the `ci-results` branch.

## Layout

```
backend/risp/      the application: api, services, stats, secondary (data sources and agent), tools (runners),
                   scripts, reports, ai, billing.py, web (public site)
backend/migrations ordered SQL migrations (applied automatically on start)
backend/tests/     unit, integration and browser tests with reference fixtures; fake Stripe for billing tests
frontend/          the web app (ES modules, no build step) and styles
deploy/            Dockerfile, Compose + Caddy, systemd units, installers, env.example
docs/              ARCHITECTURE.md (design, validation, security, roadmap), DEPLOYMENT.md
```

## Status and limits

**Not built yet.** These are marked as planned in the app:

- repeated-measures and factorial ANOVA;
- ordinal and multinomial regression;
- panel and time-series models, mediation and moderation, survival analysis, SEM.

**Payments.** The billing code is tested against an imitation of Stripe's API, including a browser test of the
whole purchase. It hasn't yet been run against a real Stripe account. Do that in Stripe's test mode before going
live (steps in DEPLOYMENT.md). Stripe only opens accounts for businesses registered in the countries it supports.

**Yahoo Finance.** Yahoo's terms restrict automated and commercial use. Review them for your use case.

**Scale.** One app process serves a site, with SQLite. That suits research groups and departments; PostgreSQL is
the next step for larger installations.

**Legal pages.** The terms and privacy pages are drafts for the operator to review with a lawyer.
