# Fixora 2.0 — AI-Assisted Data Reliability, Investigation & Repair Platform

**Tagline:** *Fix the noise. Find the truth.*

Fixora is an evidence-driven data reliability platform that detects data quality defects, investigates their root causes, compares alternative counterfactual repair strategies, predicts potential downstream effects, validates new dataset versions, and maintains a transparent, tamper-evident audit trail.

---

## 1. Core Principles

1. **Never silently modify uploaded data:** Original uploaded files are preserved permanently as immutable source snapshots.
2. **Human approval for every repair:** Every modification must be previewed side-by-side with cell-level diffs before application.
3. **Reproducible detection:** Every issue is backed by deterministic rules and verifiable empirical evidence.
4. **Distinguish facts from hypotheses:** Scan findings represent empirical facts. Root-cause clusters and AI suggestions are explicitly labeled as hypotheses.
5. **Reversible lineage:** Approved repairs spawn new immutable versions (`v1` → `v2` → `v3`). Rollbacks create fresh versions without destroying historical records.
6. **Zero AI dependency:** Complete platform functions deterministically without external AI API credentials.

---

## 2. Flagship Features

- **Data Detective & Root Cause Hypotheses:** Groups related defects into clusters (e.g., casing drift across department taxonomies, duplicate student ID collisions, legacy date formats, missing packages on confirmed placements).
- **Counterfactual Repair Lab:** Compare alternative repair strategies (Canonical Normalization, Statistical Median Imputation, Deduplication, Flag for Review) in an isolated sandbox with cell-level diff previews.
- **Blast-Radius Analysis:** Computes ripple effects on downstream derived metrics (e.g., departmental placement rate, average CTC) and reporting artifacts before approving any repair.
- **Data Drift Radar:** Compares dataset versions or uploads to track missingness deltas, category distribution shifts, and quantile changes without mistaking valid updates for errors.
- **Adaptive Rule Builder:** Test and configure custom validation rules (numeric bounds, allowed categories, required fields, unique keys) on live datasets.
- **Cross-Dataset Consistency:** Reconcile entity records across independent tables (e.g. Placement Cohort vs Central Enrollment Register).
- **Data Reliability Firewall:** Pre-flight ingestion gate that inspects schema conformance, quarantines defective files, and supports authorized investigator overrides.
- **Verifiable Trust Passport:** Generates an empirical certificate featuring a cryptographic SHA-256 fingerprint, documented scoring formulas, and immutable audit logs.
- **Lineage Time Machine:** Complete version graph with parent/child relationships and non-destructive rollbacks.

---

## 3. Technology Stack

- **Frontend:** React 19, TypeScript, Vite, Tailwind CSS v4, Lucide Icons
- **Backend:** Express, Node.js, TypeScript (`tsx`), PapaParse, XLSX, Cryptographic SHA-256 Engine
- **AI Integration (Optional):** Google Gemini 2.5 Flash via `@google/genai` (sanitized evidence only; deterministic fallback active when unconfigured)
- **Testing:** Automated test suite in `tests/qualityEngine.test.ts` (16 test assertions covering end-to-end reliability workflow)

---

## 4. Getting Started

### Local Development
```bash
# Install dependencies
npm install

# Start full-stack development server (Express + Vite on port 3000)
npm run dev

# Run reliability and authentication tests
npm test

# Production build
npm run build
npm start
```

### Authentication and account setup

The application has sign-up, sign-in, forgot-password, and reset-password screens backed by the Express API. Passwords are bcrypt-hashed, sessions use opaque HttpOnly cookies, and users, session records, and hashed reset tokens are persisted in SQLite. The database is created on first start and schema migrations are applied automatically. Dataset and analysis functionality remains behind authenticated API routes.

Copy `.env.example` to `.env` and configure:

| Variable | Purpose | Default |
|---|---|---|
| `NODE_ENV` | Set to `production` to enable Secure session cookies and disable development reset-link logging. | `development` |
| `PORT` | Express/Vite application port. | `3000` |
| `APP_URL` | Public base URL used in password reset links. | `http://localhost:3000` |
| `DATABASE_PATH` | SQLite database file for accounts, sessions, and reset tokens. | `./data/fixora.sqlite` |
| `SMTP_HOST` | SMTP server hostname; configure with the SMTP variables below to deliver reset emails. | unset |
| `SMTP_PORT` | SMTP port; port 465 uses TLS. | `587` |
| `SMTP_USER` / `SMTP_PASS` | SMTP authentication credentials. | unset |
| `SMTP_FROM` | Verified sender address for reset email. | unset |
| `GEMINI_API_KEY` | Optional AI explanation assistant. | unset |

When SMTP is not configured in development, reset links are printed to the **server console** for local testing; the application does not claim that an email was sent. Configure all four SMTP values (`SMTP_HOST`, `SMTP_USER`, `SMTP_PASS`, `SMTP_FROM`) to deliver email. In production, reset tokens are never written to logs or returned by the API; SMTP must be configured for users to receive password reset links.

Registration requires a name, a unique valid email, acceptance of the terms, and a password of 10–128 characters containing uppercase, lowercase, a number, and a symbol. Sign-in, registration, and reset requests are rate limited. Sessions expire after 12 hours, or 30 days when “Remember me” is selected. Reset links expire after one hour, are stored as hashes, and can be used once.

SQLite stores authentication records only. Fixora’s existing dataset engine currently keeps uploaded/demo datasets and analysis state in its existing in-memory storage, and that data remains shared across authenticated accounts; account-specific dataset ownership and durable dataset storage are not implemented by this authentication change.

### Optional AI Environment Variable
Set the following in `.env` or system environment:
```env
GEMINI_API_KEY=your_api_key_here
```
Fixora operates with 100% feature completeness even with zero AI API keys configured.
