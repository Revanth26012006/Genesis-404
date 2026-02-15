# requirements.md
## Project: JanAI – Voice-First Civic Truth & Services Assistant

### 1. Overview
JanAI is a multilingual, voice-first civic assistant that:
- Fact-checks misinformation and civic/political claims
- Verifies government schemes, public services, and opportunities
- Provides trusted evidence with citations
- Works in low-bandwidth environments
- Supports voice input/output for inclusion and accessibility

This system targets citizens, students, job seekers, community workers, and public helpdesk staff.

---

### 2. Goals
- Reduce misinformation spread in communities.
- Improve access to verified government information, schemes, and services.
- Enable voice-first interaction in Indian languages.
- Provide citation-backed verification and next-step guidance.
- Ensure usability in low-bandwidth and low-literacy contexts.

---

### 3. Non-Goals
- Replacing official government portals.
- Real-time breaking news moderation for all social media platforms.
- Guaranteeing 100% truth classification for all claims (system will support “Unverified/Insufficient Evidence”).

---

### 4. Target Users & Use Cases
#### 4.1 Primary Users
- Rural & semi-urban citizens
- Students (scholarships/exams)
- Job seekers (recruitment alerts)
- Farmers & small vendors (schemes/resources)
- Community workers/NGOs
- Public helpdesk/CSC staff

#### 4.2 Core Use Cases
- **UC1:** User pastes a WhatsApp forward → system returns verdict + citations.
- **UC2:** User speaks a question in Telugu/Hindi → system replies in same language.
- **UC3:** User asks about a scheme → system provides eligibility + documents + official link.
- **UC4:** User shares a suspicious link → system flags scam risk + suggests official alternatives.
- **UC5:** User asks for verified opportunities → system lists trusted sources.

---

### 5. Functional Requirements
#### 5.1 Input & Interaction
- FR1: System shall accept text input.
- FR2: System shall accept voice input.
- FR3: System shall support at least Telugu, Hindi, and English in MVP.
- FR4: System shall allow users to select language manually.
- FR5: System shall provide voice output for responses (optional but recommended).

#### 5.2 Claim Extraction
- FR6: System shall extract 1–3 atomic claims from input text.
- FR7: System shall detect whether the input is a “fact-check request” or a “service guidance request.”

#### 5.3 Evidence Retrieval
- FR8: System shall retrieve evidence using a web search API (Serper.dev or Bing).
- FR9: System shall prioritize trusted domains (.gov.in, pib.gov.in, who.int, un.org, reuters.com, bbc.com, thehindu.com).
- FR10: System shall store retrieved evidence snippets for transparency and auditing.

#### 5.4 Evidence Ranking
- FR11: System shall rank retrieved evidence using semantic similarity (SentenceTransformers).
- FR12: System shall select top-k evidence snippets (default k=5).

#### 5.5 Verification / Fact Checking
- FR13: System shall classify each claim as Supported / Refuted / Neutral.
- FR14: System shall aggregate evidence to output a final verdict:
  - True
  - False
  - Misleading
  - Unverified (Insufficient Evidence)
- FR15: System shall output a Truth Score (0–100).

#### 5.6 Explanation & Citations
- FR16: System shall provide a short explanation (2–5 lines).
- FR17: System shall include citations (URLs) for each evidence snippet.
- FR18: System shall provide a “shareable result summary” suitable for WhatsApp forwarding.

#### 5.7 Civic Service & Scheme Guidance
- FR19: System shall maintain a curated knowledge base of at least 10–15 schemes/services for MVP.
- FR20: System shall provide:
  - Eligibility
  - Required documents
  - Steps to apply
  - Official portal link
  - Helpline / office guidance (if available)

#### 5.8 Scam / Fake Link Detection
- FR21: System shall detect suspicious URLs (shorteners, non-official domains, payment traps).
- FR22: System shall warn users and recommend official sources.

#### 5.9 Low-Bandwidth Mode
- FR23: System shall support a “compact response mode”:
  - 1-line verdict
  - 1–2 evidence links
  - Minimal formatting

#### 5.10 Logging & Admin
- FR24: System shall log user queries and system verdicts for evaluation.
- FR25: System shall allow an admin to update scheme knowledge base.

---

### 6. Data Requirements
- DR1: Curated dataset of schemes/services (JSON/Firestore).
- DR2: Trusted domain allowlist.
- DR3: Query logs (anonymized).
- DR4: Evidence snippets with source URLs.
- DR5: Optional: FEVER dataset for NLI baseline testing.

---

### 7. System Requirements
#### 7.1 Performance
- SR1: MVP response time should be under 8–12 seconds for a typical query.
- SR2: System should support at least 50 concurrent users in pilot mode.

#### 7.2 Reliability
- SR3: If evidence retrieval fails, system shall return “Unverified” with explanation.
- SR4: System shall handle invalid URLs gracefully.

#### 7.3 Security & Privacy
- SR5: System shall not store personal identity information.
- SR6: Logs shall be anonymized.
- SR7: System shall not provide political persuasion; it only verifies claims using evidence.

#### 7.4 Accessibility
- SR8: System UI shall support large fonts and simple layouts.
- SR9: System shall support voice interaction for low-literacy users.

---

### 8. Constraints
- C1: Must be feasible as a hackathon MVP.
- C2: Must use trusted evidence sources.
- C3: Must support multilingual + voice-first.
- C4: Must function in low bandwidth environments.

---

### 9. Acceptance Criteria
- AC1: Given a WhatsApp forward claim, system outputs verdict + truth score + citations.
- AC2: Given a scheme query, system outputs eligibility + steps + official link.
- AC3: Voice input works in Telugu/Hindi/English.
- AC4: Low-bandwidth mode produces a compact response.
- AC5: Scam link detector flags suspicious links reliably.

---

### 10. Future Enhancements
- OCR for poster screenshots.
- More Indian languages.
- District-wise personalization.
- WhatsApp bot at scale.
- Community reporting and misinformation heatmap.
