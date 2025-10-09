# ✅ Finalized MVP PRD (Version 1.0)

## 🧭 North Star
Provide Olympia management with a trusted, one-glance understanding of changes between two Westnet BoD reports — financially, narratively, and psychologically — to enhance meeting preparedness.

## 🎯 MVP Scope (Final)
The MVP will analyze two consecutive BoD PDFs (e.g., July vs November 2024) and will:
- Extract and align financial data from tables or screenshots.
- Highlight numerical variances automatically (revenues, margins, etc.).
- Detect commitment shifts (promises made vs status reported).
- Detect tone/confidence changes in management statements (sentence-level, color-coded).
- Identify omissions (topics present before but now missing).
- Generate a per-section narrative summary explaining key differences.
- Provide clickable citations (page/slide reference).
- Display all this in a web dashboard (summary + side-by-side diff).

## 🧩 MVP Feature Map
| Layer                     | Feature                     | Description                                                                                     |
|---------------------------|-----------------------------|-------------------------------------------------------------------------------------------------|
| 1. Ingestion & Parsing    | PDF Upload & Parsing        | Analyst uploads two reports. The system parses structure (titles, tables, text, OCR if image-based). |
| 2. Section Alignment      | Auto-section matching       | Match sections by semantic similarity (e.g., “Financial Overview” ↔ “Financial Highlights”).   |
| 3. Financial Variance Engine | Numeric detection         | Extract financial tables; compute Δ%; fallback to OCR for screenshots; output as variance table. |
| 4. Commitment Tracker     | “We will / plan / target” pattern | Detect commitments, align updates, highlight broken/fulfilled commitments.                      |
| 5. Tone & Sentiment Diff  | Word-level change highlighting | Detect softened verbs (“will → hope to”) and confidence shifts. Color-coded sentence highlights. |
| 6. Omission Detector      | Topic disappearance         | Identify topics/keywords from prior report not found in current one.                           |
| 7. Narrative Generator     | Per-section summary        | Concise 2–3 sentences per section explaining changes and notable differences.                  |
| 8. Dashboard (Option C)   | Split layout UI            | Top = AI summary per section. Bottom = side-by-side comparison view with highlights and variance tables. |
| 9. Citations Engine       | Reference traceability      | Every insight links to “page, slide, section.”                                                |
| 10. Export Report         | BoD Brief Generator        | Generate printable executive prep report (summary + questions).                                |

## ⚙️ MVP Technical Stack
| Component                 | Tech                                                                          |
|---------------------------|-------------------------------------------------------------------------------|
| Backend                   | FastAPI (Python)                                                             |
| Frontend                  | React + Tailwind (simple two-pane UI)                                        |
| Parsing / OCR             | PyMuPDF + Tesseract + layoutparser                                           |
| NLP / LLM                 | OpenAI GPT-4-Turbo (via API) for section summaries + tone analysis           |
| Data Alignment            | sentence-transformers + cosine similarity                                      |
| Storage                   | SQLite (local cache)                                                         |
| Deployment                | Docker container — runs on local server or private cloud                      |
| Privacy                   | All PDFs stored locally, deletable post-session                               |

## 🧠 MVP Outputs
| Output Type               | Example                                                                       |
|---------------------------|-------------------------------------------------------------------------------|
| Numeric Variance Table    | Revenue: +12%, EBITDA: -3%, Cash Flow: -8%                                   |
| Commitment Tracker         | “We will open the new logistics center by Q4” → now “We are exploring options for expansion.” (Tone: downgraded) |
| Tone Highlights            | Orange highlight on softened verbs, green on confident language.              |
| Omission Alerts           | “Digital Transformation” section missing in new deck.                        |
| Narrative Summary         | “Profitability improved despite declining gross margins. Management confidence moderated regarding supply chain improvements.” |

## 🧾 Deliverables (Functional Prototype)
- Upload interface
- Parsing & comparison pipeline
- Per-section summary + numeric variance detection
- Side-by-side diff viewer (color-coded + citations)
- Exportable report (PDF brief)

## 💡 Success Criteria
- ✅ Reduces prep time by at least 60%
- ✅ Detects ≥80% of meaningful changes manually identified by analysts
- ✅ Finds ≥1 “missed” issue from manual review
- ✅ Clear, verifiable output with slide references
- ✅ Executives say “This saves us time and makes us sharper”