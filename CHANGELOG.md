# Changelog

## 1.4.0

### Surveys: three ways to create
- **Start from scratch**, **upload a questionnaire** (Word, PDF or text, read and converted), or **system-assisted**:
  enter the topic, objectives, research design and respondents, and Statvane searches online for published, validated
  scales and drafts the questionnaire from them.
- Sources are checked: a scale's link is kept only if it came from the web search for that request; invented links are
  removed and flagged.
- Copyright: items are used word-for-word only from scales the source says are free to use (and whose source was
  verified). For licensed or unclear scales the items are new wording, marked "modelled on". When unsure: licensed.
- Respondents see only the questions. The researcher's **Scale sources (Word)** download names and cites every scale,
  with its link and licence status, and shows which items came from which scale.
- Needs an AI provider that can search the web (Anthropic). The curated scale library from 1.3 is removed.

### Plans, credits and account sharing
- Plans: Free (manual surveys, 3 questionnaire uploads a month), Individual ($30/month), Team ($100/month, 5 seats).
  Prices, monthly credits, every credit cost, credit packs, upload limits and seats are set in Admin → Plans and credits.
- Data analysis, results files, system-assisted surveys and AI features need a paid plan and use credits; failed
  operations are refunded. Free users see an upgrade notice before, not an error after.
- Anti-sharing: device limit (2 by default; a new sign-in signs out the oldest), admin alerts for accounts used from many
  networks or devices (thresholds in Settings), and Team plans where each member has their own login and shares credits.

### Fixes
- Browsers now re-check the app's files on every load, so updates appear without a hard refresh.
- Packages no longer include a development secret key.

### Infrastructure
- Rate limits shared across workers through Redis (`RISP_REDIS_URL`); falls back to in-process, fails open.
- Script sandbox: no network, write-isolated, and runs as an unprivileged user (`RISP_SANDBOX_UID`).
- Security fixes from the backend audit (see docs/SECURITY.md).

## 1.3.0

### New tests (11), each checked against R
| Test | Checked against |
|---|---|
| Factorial ANOVA and ANCOVA (Type III, partial eta squared, Levene) | `lm` + `drop1(test = "F")` with sum-to-zero contrasts |
| Repeated-measures ANOVA, optional between-subjects factor (Mauchly; Greenhouse–Geisser, Huynh–Feldt, lower-bound) | `mauchly.test`, `anova.mlm`, `aov(... + Error())` |
| Ordinal regression (logit, probit; thresholds, odds ratios, pseudo R²) | `MASS::polr` |
| MANOVA (Pillai, Wilks, Hotelling–Lawley, Roy; follow-up ANOVAs) | `summary.manova` |
| Multinomial logistic regression | `nnet::multinom` |
| Hierarchical (blockwise) linear regression (R² change, F change) | `anova(m1, m2)` |
| Hierarchical cluster analysis (Ward, average, complete, single) | `hclust` (Ward = `ward.D2`) |
| Discriminant analysis (eigenvalues, canonical correlations, Wilks, classification) | `MASS::lda` and the W⁻¹B eigenvalues |
| Kaplan–Meier with log-rank test | `survival::survfit`, `survdiff` |
| Cox regression (Breslow or Efron ties) | `survival::coxph` |
| Multiple imputation with pooled regression (Rubin's rules, Barnard–Rubin df) | Pooling formulas tested directly; seeded and reproducible |

Every new test has scripts in R, Python, SPSS syntax and Stata; jamovi (jmv) scripts where jamovi's built-in
analyses include the method. The R and Python scripts are run in the test suite.

### Also in 1.3
- Each tool's menus now include all of these tests; nothing that runs is marked "coming soon".
- Home screen after sign-in with three main actions and a dashboard.
- Automatic data preparation (missing-value codes, duplicates, labels, types, reverse-scored items, scale scores, outliers).
- Data analysis is tool-first: tool → analysis → data and variables → results, with per-test missing-value and outlier rules.
- New dependency: statsmodels 0.15 (SciPy 1.16+). The R engine installer adds MASS, nnet, survival and mice.

### Still planned
Post hoc tests for factorial ANOVA (Tukey, Games–Howell), mediation and moderation, MANCOVA, the parallel-lines test for ordinal
regression, Box's M, and Little's MCAR test.

## Security (audit)
- Script sandbox: analyses run with no network and write-isolated to their own folder when bubblewrap is installed.
- Fixed: R Markdown/script injection via analysis names and AI text; spoofable client IP (RISP_TRUST_PROXY); SSRF in AI provider URLs; non-constant-time CSRF compare.

## Scalability and sandbox (this round)
- Rate limiting can use Redis (RISP_REDIS_URL) to share limits across workers; falls back to in-process, fails open if Redis is down.
- The script sandbox now also drops each tool to an unprivileged uid (RISP_SANDBOX_UID, default nobody) with no capabilities, with automatic fallback on kernels without user namespaces.

