# design.md
## Project: JanAI – Voice-First Civic Truth & Services Assistant

### 1. System Design Summary
JanAI is designed as a modular AI pipeline combining:
- Voice + text input
- Claim extraction
- Evidence retrieval from trusted sources
- Evidence ranking
- Verification (NLI/LLM)
- Response generation with citations
- Civic scheme guidance
- Scam link detection
- Low-bandwidth mode

---

### 2. Architecture (High Level)
#### 2.1 Components
1. **Client (Web / Mobile)**
   - Text input
   - Voice input
   - Language selector
   - Result view (verdict, score, citations, action steps)

2. **Backend API (FastAPI)**
   - Orchestrates the pipeline
   - Handles search calls
   - Stores logs
   - Serves scheme knowledge base

3. **AI Services**
   - Speech-to-text (Whisper / Google STT)
   - LLM for claim extraction + explanation
   - Semantic ranker (SentenceTransformers)
   - Verifier (NLI model OR LLM verifier)

4. **Data Layer**
   - Firebase Firestore (schemes + logs)
   - Cache for repeated queries

---

### 3. Detailed Pipeline Design
#### 3.1 Input Handling
- Text is passed directly to claim extraction.
- Voice is converted using STT → text pipeline.
- Language is detected automatically but user can override.

#### 3.2 Claim Extraction Module
**Purpose:** Convert noisy forwards into atomic claims.

**Approach:**
- LLM prompt: “Extract 1–3 factual claims. Keep short.”
- Output JSON format:
  - claims: []
  - intent: FACT_CHECK or SERVICE_GUIDE

#### 3.3 Evidence Retrieval Module
**Purpose:** Fetch evidence from trusted sources.

**Approach:**
- Generate a search query for each claim.
- Call Serper.dev/Bing Search API.
- Filter results by allowlist domains.
- Store title, snippet, URL.

#### 3.4 Evidence Ranking Module
**Purpose:** Select the most relevant evidence.

**Approach:**
- Use SentenceTransformers embeddings (MiniLM).
- Compute similarity between claim and evidence snippets.
- Select top-k = 5.

#### 3.5 Verification Module (NLI/LLM)
**Purpose:** Determine if evidence supports or refutes claim.

**Option A (Hackathon recommended):**
- LLM verifier:
  - Input: claim + evidence snippets
  - Output: supported/refuted/neutral + confidence

**Option B (Pure ML baseline):**
- HuggingFace DeBERTa-v3 MNLI
- Pairwise inference per evidence snippet

#### 3.6 Aggregation & Truth Score
- Evidence votes aggregated.
- Rules:
  - Majority support → True
  - Majority contradiction → False
  - Mixed signals → Misleading
  - No strong evidence → Unverified
- Truth Score:
  - True: 75–100
  - False: 0–25
  - Misleading: 25–60
  - Unverified: 40–55 (low confidence)

#### 3.7 Response Generator
Outputs:
- Verdict label
- Truth Score
- Explanation (simple language)
- Citations list
- Next steps (if civic/scheme)

#### 3.8 Scheme & Service Guidance
- Curated dataset:
  - Scheme name
  - Eligibility
  - Documents
  - Steps
  - Official portal
  - Helpline

LLM used only for formatting into user language.

#### 3.9 Scam Link Detector
Rules:
- URL shorteners
- Non-official domains
- Payment keywords (“pay”, “fee”, “registration amount”)
- Fake TLD patterns

Outputs:
- Risk: Low / Medium / High
- Recommendation: official alternative

#### 3.10 Low-Bandwidth Mode
- Compress response:
  - verdict + 1-line reason
  - 1–2 links
  - minimal UI

---

### 4. API Design
#### 4.1 Endpoints
- POST `/api/query`
  - Input: { text, language, mode }
  - Output: verdict, truth_score, explanation, citations, actions

- POST `/api/voice`
  - Input: audio file
  - Output: same as /query

- GET `/api/schemes`
  - Returns list of supported schemes

- GET `/api/schemes/{id}`
  - Returns scheme details

- POST `/api/admin/schemes`
  - Add/update scheme (admin only)

---

### 5. Data Model
#### 5.1 Firestore Collections
- `schemes`
  - id, name, eligibility, documents, steps, official_link, helpline

- `queries`
  - id, timestamp, language, input_text, claims, verdict, truth_score

- `evidence`
  - query_id, claim, url, snippet, label

---

### 6. UI Design (MVP)
#### 6.1 Pages
1. Home
   - mic button
   - paste input box
   - language selector

2. Result
   - verdict card
   - truth score
   - evidence list
   - share summary

3. Schemes
   - search schemes
   - view steps + official link

4. Admin (optional)
   - add/update schemes

---

### 7. Deployment Design
- Frontend: Vercel
- Backend: Render/Railway
- Firestore: Firebase
- Models:
  - Hosted API calls (Gemini/GPT)
  - Optional local HF model for NLI

---

### 8. Risks & Mitigations
- **Hallucination risk:** restrict responses to evidence + citations.
- **Search noise:** allowlist trusted domains + ranking.
- **Latency:** caching + top-k retrieval.
- **Bias concerns:** evidence-based only; no political persuasion.
- **Low connectivity:** compact mode + fewer API calls.

---

### 9. MVP Build Plan (Hackathon)
Day 1:
- Frontend UI + backend skeleton

Day 2:
- Search + evidence retrieval + ranking

Day 3:
- Verification + verdict + citations

Day 4:
- Scheme KB + multilingual responses

Day 5:
- Voice support + final polish + demo

---

### 10. Future Roadmap
- OCR for posters/screenshots
- WhatsApp bot deployment
- More languages
- District-wise personalization
- Community reporting and misinformation heatmap
