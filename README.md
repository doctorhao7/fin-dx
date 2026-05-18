# FinDX — Financial Digital Transformation Dashboard

An interactive proposal dashboard that visualizes four DX (digital transformation) initiatives for a financial institution. This project re-organizes it into a data-driven web app where every KPI, customer, document, automation, and roadmap entry is editable through a single configuration file.

## What it demonstrates

The dashboard presents four solutions, each on its own panel.

### 1. AI Loan Underwriting (`AI融資審査`)
A real-time credit scoring sandbox. Move the sliders for income, debt, tenure, credit score, and loan amount and watch the gauge, decision band (auto-approve / human-review / auto-reject), interest rate, and SHAP factor breakdown update live. Modeled on an XGBoost / LightGBM ensemble with a SHAP explainability layer for regulator-facing transparency.

- **Headline metrics:** 87% auto-decision rate, 4.2h average review time (-62%), AUC 0.91, default rate -0.6 pt.
- **Compliance:** SHAP value logging on every decision, weekly Equalized Odds bias audit, FSA AI guideline review in progress.

### 2. Paperless Operations (`ペーパーレス化`)
An OCR + AI document processing demo. Click any document card (financial statement, registration certificate, tax certificate, handwritten loan application, trial balance) to simulate AI extraction and watch the structured output appear. The right side shows the five-stage workflow (intake → OCR/extraction → reconciliation → e-signature → immutable archive) and the cost-reduction breakdown.

- **Headline metrics:** 95.3% OCR accuracy, -78% processing labor (320k hours/year), 100% audit trail coverage.
- **Stack:** LayoutLM v3, GPT-4V, Apache Kafka, Delta Lake, RPA (UiPath), legally-compliant e-signature, PII auto-masking.

### 3. CRM Sales Enablement (`CRM営業支援`)
A Next-Best-Action engine. The customer list is sorted by AI priority score; clicking a customer surfaces tailored proposals (working capital, FX hedging, capex loan, etc.) with a "why" line citing the signals that triggered them, alongside the action timeline. The signal engine watches for fiscal-year-end approach, cash-flow shifts, capex signals, M&A news, hiring trends, and competitor activity.

- **Headline metrics:** +38% close rate, -52% wasted visits, 91% CSAT (+15 pt), 3.2× rep productivity.

### 4. Process Automation (`業務自動化`)
A control panel for 143 production automations spanning loan-application triage, credit-bureau API ingestion, draft credit-memo generation, financial-ratio reporting, follow-up scheduling, delinquency early-warning, BoJ reporting, and FX alerts. Each can be toggled, and the chart shows the monthly hours-saved trend climbing from 12k to 32k.

- **Headline metrics:** 99.4% success rate, 320k hours / ¥1.9B saved annually, ¥3.2B cumulative.
- **Roadmap:** Phase 1 (0-6mo) quick wins → Phase 2 (6-12mo) full rollout + FSA review → Phase 3 (12-24mo) generative AI agents, real-time risk monitoring, RL-driven sales optimization, differential privacy.

### Sidebar
The persistent left rail summarizes the cross-program KPIs (¥3.2B annual savings, 340% three-year ROI, 14-month payback) and shows phase status indicators.

## Project structure

```
fin_dx/
├── README.md              # This file
├── index.html             # Entry point (structural skeleton only)
├── config.json            # All data: KPIs, customers, automations, roadmap, ROI
├── css/
│   └── main.css           # Styles
├── js/
│   └── app.js             # Loads config.json and renders every panel
└── findx_solution.html    # Original single-file prototype (kept for reference)
```

The split makes it possible to:

- Edit numbers, labels, customer names, signals, etc. by touching only `config.json`.
- Restyle without reading 900 lines of inline `<style>`.
- Swap in a real backend later — `app.js` already loads its data via `fetch('./config.json')`, so pointing it at an API is a one-line change.

## Running locally

`fetch('./config.json')` does not work from `file://` due to browser CORS rules. Serve the directory with any static server:

```bash
# Python (no install needed on macOS)
python3 -m http.server 8000

# Or Node
npx serve .
```

Then open `http://localhost:8000/`.

The original `findx_solution.html` still works directly from the filesystem if you want a no-server preview.

## Editing the dashboard

Everything user-visible lives in `config.json`. The top-level keys map to:

| Key | What it controls |
|---|---|
| `meta` | Page title, header logo text, export button label |
| `sidebar.miniKpis` | The 2×2 KPI grid in the sidebar |
| `sidebar.roi` | The three ROI pills (annual savings, 3yr ROI, payback) |
| `sidebar.phases` | Phase status indicators |
| `solutions[]` | One entry per panel — eyebrow, title, description, nav label, four KPI cards |
| `loanScorer` | Slider ranges, industry risk factors, scoring weights, decision thresholds |
| `modelArchitecture` | The five feature-importance bars and tag chips |
| `documents` | Cards in the paperless demo (name, icon, simulated OCR output) |
| `paperlessSavings` | Cost-reduction bar values |
| `customers` | Customer rows (name, score, tag) |
| `nbaByCustomer` | Next-Best-Action proposals per customer |
| `timelinesByCustomer` | Action history per customer |
| `signals` / `paperlessTechStack` | Tag chips |
| `automations` | The toggleable automation list |
| `roadmap` | Implementation phases |
| `roi` | Three-year ROI table |
| `charts` | Monthly auto-decision rate, monthly automation hours saved |

Add a new customer? Append to `customers[]` and add a matching entry to `nbaByCustomer[]` and `timelinesByCustomer[]`. Add a new automation? Append to `automations[]` — the toggle UI is generated from the array.

## Notes on the simulated logic

The loan scorer in `app.js` is a deterministic hand-tuned model intended for the demo only — it weights `income/debt`, credit score, tenure, industry factor, and DSR (debt service ratio). The weights live in `config.json` under `loanScorer.weights` so you can tune the demo without touching code. None of this code should be used for actual underwriting.
