# Product Requirements Document — Insurance Claim Assistant

**Version:** 1.0  
**Date:** 2025-01-01  
**Author:** Product Team  
**Status:** Approved for v1 Development

---

## 1. Executive Summary

Insurance Claim Assistant is an AI-powered web application that guides claimants and claims professionals through the end-to-end claims lifecycle across three lines of business: **Auto**, **Homeowners**, and **General Liability**. The system reduces time-to-FNOL, organizes evidence, surfaces policy language in plain English, and delivers next-best-action recommendations — embedding AI at every stage without replacing the adjuster's judgment or rendering legal/coverage opinions.

---

## 2. Personas

### 2.1 Claimant (Primary)
| Attribute | Detail |
|-----------|--------|
| Who | Policyholder or third-party claimant filing a loss |
| Goals | File FNOL quickly, understand what happens next, track their claim without calling the carrier |
| Pain points | Confusing policy language, opaque claim stages, evidence requirements unclear, long hold times |
| Tech comfort | Consumer-grade; mobile-first expectations |
| Domain knowledge | Low — needs jargon-free guidance |

### 2.2 Claims Adjuster / Reviewer (Secondary)
| Attribute | Detail |
|-----------|--------|
| Who | Staff or independent adjuster assigned to the claim |
| Goals | Triage incoming FNOLs, review AI-generated summaries, manage reserves, document liability/damages decisions |
| Pain points | Evidence scattered across emails/uploads, repetitive narrative drafting, keeping track of multi-party claims |
| Tech comfort | High; uses claims management systems (Guidewire, Duck Creek, etc.) daily |
| Domain knowledge | High — expects correct FNOL, BI/PD, ACV/RCV, subrogation, SIU referral, ULAE/ALAE terminology |

### 2.3 Broker / Agent (Tertiary)
| Attribute | Detail |
|-----------|--------|
| Who | Producer or agent submitting a loss on behalf of their insured |
| Goals | Submit FNOL, attach policy documents, get status updates for their client |
| Pain points | Duplicate data entry across carrier portals, lack of status visibility |
| Tech comfort | High |
| Domain knowledge | Medium — understands coverage parts but defers on liability decisions |

---

## 3. User Stories & Acceptance Criteria

### 3.1 Claim Filing — FNOL Intake

**US-001: Dynamic Intake Wizard**  
*As a claimant, I want to be guided through a step-by-step FNOL form tailored to my line of business, so I don't miss required information.*

Acceptance Criteria:
- Wizard presents LOB selector (Auto / Homeowners / GL) on step 1
- Auto LOB collects: date/time of loss, loss location, VIN, involved vehicles, injury flag (BI), property damage flag (PD), police report number, witness info
- Homeowners LOB collects: loss date, peril type (fire/water/wind/theft/vandalism/other), affected areas, estimated replacement cost, occupancy type, mortgage holder
- GL LOB collects: loss date, occurrence location, claimant info, alleged injury/property damage, coverage trigger (occurrence vs. claims-made)
- Required fields enforced with client-side and server-side validation
- Draft saved after each step (no data loss on browser refresh)
- On submit: FNOL record created, claim number assigned (format: `CLM-{YYYY}-{6-digit-seq}`), confirmation email triggered

**US-002: FNOL Draft Generation**  
*As an adjuster, I want the system to auto-draft the FNOL narrative from intake answers, so I can edit rather than write from scratch.*

Acceptance Criteria:
- AI-generated FNOL narrative draft available within 30 seconds of claim submission
- Draft uses standard claims narrative format: Date of Loss, Insured, Loss Location, Description of Occurrence, Alleged Damages, Coverage Parts Implicated
- Draft clearly marked "AI Draft — Review Required" and not surfaced to claimant
- Adjuster can accept, edit, or regenerate draft
- Final draft stored in `ai_artifacts` with model version, token count, and prompt hash

---

### 3.2 Evidence Management

**US-003: Evidence Upload & Tagging**  
*As a claimant or adjuster, I want to upload photos, documents, and videos and tag them to specific claim segments, so the evidence is organized for review.*

Acceptance Criteria:
- Accepted formats: JPEG, PNG, HEIC, PDF, MP4, MOV, DOCX (max 50 MB per file)
- Upload triggers background OCR job for PDF/image files
- OCR text stored on `evidence_items.ocr_text`; AI summary generated and stored on `evidence_items.ai_summary`
- Tags: `police_report`, `medical_record`, `repair_estimate`, `photos_damage`, `photos_scene`, `recorded_statement`, `police_report`, `coverage_document`, `other`
- Evidence linked to specific claim segment (BI, PD, Coverage, Liability, Damages)

**US-004: Evidence Summarization**  
*As an adjuster, I want an AI summary of uploaded evidence, so I can triage large document sets faster.*

Acceptance Criteria:
- Summary available within 60 seconds of OCR completion
- Summary identifies: document type, key dates, monetary amounts, named parties, and flags potential SIU indicators
- PII redacted before summary is logged to audit trail
- Summary editable by adjuster

---

### 3.3 Policy Explainer

**US-005: Plain-English Policy Breakdown**  
*As a claimant, I want to paste policy language and receive a plain-English explanation, so I understand what is and isn't covered.*

Acceptance Criteria:
- Text input accepts up to 10,000 characters
- AI response sections: Coverage Summary, What's Covered, What's Excluded, Key Definitions, Deductible/Limits Summary, Questions to Ask Your Adjuster
- Response includes citations: e.g., "Per Section II, Coverage A…"
- Response includes disclaimer: "This is an informational summary only and does not constitute legal advice or a coverage determination."
- PII stripped from policy text before LLM call
- Response cached by content hash; identical text returns cached result

---

### 3.4 Claim Status & Timeline

**US-006: Status Board**  
*As a claimant, I want to see where my claim is in the process and what comes next, so I'm not in the dark.*

Acceptance Criteria:
- Status stages displayed in order: `Reported → Investigation → Coverage Evaluation → Liability/Damages Assessment → Settlement Negotiation → Closed`
- Current stage highlighted; completed stages shown as done; future stages shown as pending
- Each stage shows: estimated entry date, estimated completion date (per timeline model), responsible party (claimant action vs. adjuster action)
- AI-generated rationale for current stage delay/acceleration available on demand

**US-007: Timeline Estimation**  
*As a claimant, I want an estimated timeline for each stage of my claim, so I can plan accordingly.*

Acceptance Criteria:
- Timeline estimates vary by LOB, coverage complexity, injury flag, and state jurisdiction
- Estimates expressed as ranges (e.g., "3–7 business days") not point estimates
- Rationale displayed: "Auto PD claims without dispute typically resolve in 10–21 days in NC. Your claim involves an injury (BI) component, which adds 30–90 days for medical documentation."
- State statute of limitations surfaced: "NC auto injury SOL: 3 years (NCGS § 1-52(5))"
- Estimates not presented as guarantees; legal disclaimer attached

---

### 3.5 Next-Best-Action Recommendations

**US-008: AI Next Actions**  
*As a claimant or adjuster, I want the system to tell me what I should do next, so I don't miss a step.*

Acceptance Criteria:
- Recommendations generated per role (claimant vs. adjuster)
- For claimant: "Upload repair estimate from licensed shop," "Provide recorded statement by [date]," "Your 30-day notice deadline for UM/UIM is approaching"
- For adjuster: "Reserve adequacy review due," "SIU referral recommended based on evidence flags," "Subrogation potential identified — preserve right of recovery"
- Each action links to relevant claim tab or upload dialog
- Actions timestamped and dismissible; dismissed actions logged

---

## 4. Scope

### In Scope — v1
- Auto, Homeowners, General Liability LOBs
- Claimant portal (self-service FNOL, status, evidence upload, policy explainer)
- Adjuster dashboard (claim queue, FNOL draft review, AI summaries, next actions)
- AI features: FNOL drafter, evidence summarizer, policy explainer, next-actions recommender, timeline estimator, status rationale
- Evidence upload with OCR + AI summary (background job)
- Status board with stage tracking
- Single-tenant deployment (no multi-carrier white-labeling)
- JWT authentication (Auth0-ready)
- Audit logging for all AI outputs and data mutations

### Out of Scope — v1
- Payment processing / settlement disbursement
- Integration with carrier core systems (Guidewire, Duck Creek, Majesco)
- Electronic signature (DocuSign integration)
- Automated reserve setting (requires actuarial sign-off)
- Coverage determination AI (liability; system advises, doesn't decide)
- Multi-carrier/multi-tenant SaaS architecture
- Mobile native apps (iOS/Android)
- Workers' Compensation LOB
- ECOA/Reg B compliance (not applicable — this is P&C claims, not lending)

### Out of Scope — Explicitly Noted
ECOA / Regulation B governs credit underwriting decisions, not insurance claims processing. It is **not applicable** to this product.

---

## 5. Success Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| FNOL completion rate | ≥ 85% of started wizards | Events: `wizard_started` / `fnol_submitted` |
| Time to FNOL submission | ≤ 8 min median | `fnol_submitted.timestamp - wizard_started.timestamp` |
| Evidence upload OCR success rate | ≥ 95% | `ocr_status = 'complete'` / total uploads |
| Policy explainer satisfaction | ≥ 4.2 / 5.0 | In-app thumbs up/down + optional rating |
| AI FNOL draft acceptance rate (no edit) | ≥ 40% | `draft_action = 'accepted'` |
| Claimant status board DAU | ≥ 60% of active claims | Status page views / active claims |
| System uptime | ≥ 99.5% | Infrastructure monitoring |

---

## 6. Risk & Compliance

### 6.1 PII and Data Security — GLBA Safeguards Rule
The Gramm-Leach-Bliley Act Safeguards Rule (16 CFR Part 314, amended 2023) requires financial institutions — including insurance companies — to implement an information security program protecting **NPI (Non-public Personal Information)**. Requirements applicable to this system:
- Encrypt NPI at rest (AES-256) and in transit (TLS 1.3+)
- Access controls with role-based permissions
- Audit logging of all NPI access and mutation events
- Vendor risk management for LLM providers (data processing agreements required)
- Annual penetration testing and vulnerability assessment
- Designate a qualified individual responsible for the security program
- **PII redaction before LLM calls**: SSN, driver's license numbers, policy numbers, and PHI must be redacted before any text is sent to an external LLM API

### 6.2 HIPAA — Injury Claims (BI)
When a claim involves bodily injury (BI), medical records may be uploaded and processed. Under HIPAA:
- The platform operator may function as a **Business Associate** to covered entities (medical providers, health plans)
- A **Business Associate Agreement (BAA)** must be executed with any LLM provider processing PHI
- Medical records must be stored in HIPAA-eligible infrastructure (AWS, GCP, Azure all offer BAA)
- Minimum necessary standard applies: only PHI required for claim purposes should be collected
- Breach notification within 60 days per HIPAA Breach Notification Rule (45 CFR §§ 164.400–414)
- **Recommendation for v1**: Route BI medical evidence through a separate, HIPAA-hardened storage bucket; implement field-level encryption for PHI columns

### 6.3 Unfair Claims Settlement Practices — NAIC Model Act #900
All 50 states have enacted Unfair Claims Settlement Practices Acts (UCSPA) based substantially on the NAIC Model Unfair Claims Settlement Practices Act (Model Act #900). The AI system must not facilitate practices prohibited by these acts, including:
- Misrepresenting policy provisions relevant to the coverage at issue
- Failing to acknowledge and act reasonably promptly on communications
- Not attempting in good faith to effectuate prompt, fair, equitable settlements where liability is reasonably clear
- Compelling insureds to litigate by offering substantially less than amounts ultimately recovered
- Delaying investigation or payment without a reasonable basis

**AI guardrail**: The system must not generate responses that could be construed as denying coverage, misrepresenting policy terms, or advising delay. All AI outputs include disclaimers and are gated behind human review.

### 6.4 State Statute of Limitations
Timeline estimates reference SOL by state and LOB. Critical references:
| State | Auto Injury SOL | Property Damage SOL | Reference |
|-------|----------------|--------------------|----|
| NC | 3 years | 3 years | NCGS § 1-52(5) |
| CA | 2 years | 3 years | CCP § 335.1, § 338 |
| FL | 2 years (2023+) | 4 years | Fla. Stat. § 95.11 |
| TX | 2 years | 2 years | Tex. Civ. Prac. & Rem. § 16.003 |
| NY | 3 years | 3 years | CPLR § 214 |

Timeline service must source state from claim intake and apply correct SOL.

### 6.5 ECOA / Reg B — Not Applicable
The Equal Credit Opportunity Act and Regulation B govern credit decisions. This system processes P&C insurance claims and does not make credit determinations. ECOA/Reg B compliance is **not required**.

---

## 7. Phased Roadmap

### v1 — Foundation (Months 1–4)
- FNOL intake wizard (Auto, HO, GL)
- Evidence upload + OCR + AI summary
- Policy explainer
- Status board (manual stage updates)
- AI: FNOL drafter, next actions, timeline estimator
- JWT auth, audit logging, PII redaction
- Single-tenant deployment

### v1.5 — Enhanced Intelligence (Months 5–8)
- Adjuster workflow: claim queue, reserve tracking, coverage evaluation checklist
- Automated stage transitions based on evidence completeness rules
- SIU referral flag (pattern-based rules + AI signal)
- Subrogation potential scoring
- Email/SMS status notifications
- Multi-party claim support (TP claimants, attorneys, medical providers)
- Recorded statement scheduling (Calendly integration)
- Basic reporting dashboard (ALAE/ULAE tracking, cycle time by stage)

### v2 — Platform (Months 9–18)
- Carrier system integration layer (Guidewire ClaimCenter API adapter)
- Electronic signature (DocuSign/HelloSign)
- Reserve adequacy AI assistant (with actuarial sign-off workflow)
- Multi-tenant / white-label carrier portal
- Predictive litigation flag (ML model on claim features)
- Mobile-responsive PWA → native app consideration
- Advanced analytics: claim outcome prediction, settlement range modeling
- State-specific compliance engine (state filing requirements, mandatory disclosures)
