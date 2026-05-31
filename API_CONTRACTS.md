# API Contracts — Insurance Claim Assistant

**Base URL (local):** `http://localhost:8000`  
**Base URL (prod):** `https://api.claimassist.example.com`  
**Content-Type:** `application/json`  
**Auth:** `Authorization: Bearer <jwt_token>` on all protected routes

---

## Standard Error Schema

```json
{
  "error": {
    "code": "CLAIM_NOT_FOUND",
    "message": "Claim CLM-2025-000042 not found or access denied.",
    "details": null,
    "request_id": "req_01HZ9XKPQ2M3N4R5T6W7"
  }
}
```

| HTTP Status | Error Code | Meaning |
|-------------|------------|---------|
| 400 | `VALIDATION_ERROR` | Request body fails schema validation |
| 401 | `UNAUTHORIZED` | Missing or invalid JWT |
| 403 | `FORBIDDEN` | Valid JWT but insufficient role/ownership |
| 404 | `NOT_FOUND` | Resource does not exist |
| 409 | `CONFLICT` | Duplicate resource or invalid state transition |
| 422 | `UNPROCESSABLE` | Business rule violation |
| 429 | `RATE_LIMITED` | Too many requests (LLM endpoints: 20 req/min) |
| 500 | `INTERNAL_ERROR` | Unexpected server error |
| 503 | `LLM_UNAVAILABLE` | LLM provider API unavailable |

---

## /api/auth

### POST /api/auth/register

**Request:**
```json
{
  "email": "jane.doe@example.com",
  "password": "Secure!Pass9",
  "first_name": "Jane",
  "last_name": "Doe",
  "phone": "+19195551234",
  "role": "claimant"
}
```

**Response 201:**
```json
{
  "user_id": "550e8400-e29b-41d4-a716-446655440000",
  "email": "jane.doe@example.com",
  "role": "claimant",
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 3600
}
```

---

### POST /api/auth/login

**Request:**
```json
{
  "email": "jane.doe@example.com",
  "password": "Secure!Pass9"
}
```

**Response 200:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "expires_in": 3600,
  "user": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "jane.doe@example.com",
    "first_name": "Jane",
    "last_name": "Doe",
    "role": "claimant"
  }
}
```

---

### POST /api/auth/refresh

**Request:**
```json
{
  "refresh_token": "rt_eyJhbGci..."
}
```

**Response 200:** Same as login response.

---

## /api/claims

### POST /api/claims — Create FNOL

**Auth required.** Role: any.

**Request:**
```json
{
  "lob": "auto",
  "loss_date": "2025-06-15",
  "loss_time": "2025-06-15T14:32:00-05:00",
  "loss_location": "I-40 W near Exit 289, Raleigh, NC 27604",
  "loss_description": "Rear-end collision at highway speed. Other driver failed to brake. Airbags deployed.",
  "state_jurisdiction": "NC",
  "injury_flag": true,
  "lob_metadata": {
    "vin": "1HGBH41JXMN109186",
    "make": "Honda",
    "model": "Accord",
    "year": 2021,
    "plate": "ABC-1234",
    "police_report_number": "RPD-2025-04471",
    "at_fault_party": "third_party",
    "damage_type": ["PD", "BI"]
  },
  "parties": [
    {
      "party_type": "claimant",
      "first_name": "Jane",
      "last_name": "Doe",
      "phone": "+19195551234",
      "email": "jane.doe@example.com",
      "role_in_claim": "First-party insured"
    },
    {
      "party_type": "third_party",
      "first_name": "John",
      "last_name": "Smith",
      "phone": "+19195559876",
      "role_in_claim": "Adverse driver"
    }
  ]
}
```

**Response 201:**
```json
{
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "claim_number": "CLM-2025-000001",
  "lob": "auto",
  "current_stage": "reported",
  "status": "open",
  "reported_at": "2025-06-15T19:47:22Z",
  "fnol_draft_status": "queued",
  "message": "Claim filed successfully. You will receive a confirmation email shortly."
}
```

---

### GET /api/claims — List Claims

**Auth required.** Claimant sees own claims; adjuster sees assigned or all; admin sees all.

**Query params:** `?page=1&per_page=20&lob=auto&status=open&stage=investigation&adjuster_id=<uuid>`

**Response 200:**
```json
{
  "total": 127,
  "page": 1,
  "per_page": 20,
  "claims": [
    {
      "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
      "claim_number": "CLM-2025-000001",
      "lob": "auto",
      "insured_name": "Jane Doe",
      "loss_date": "2025-06-15",
      "current_stage": "investigation",
      "status": "open",
      "injury_flag": true,
      "state_jurisdiction": "NC",
      "assigned_adjuster": "Mike Torres",
      "reported_at": "2025-06-15T19:47:22Z",
      "updated_at": "2025-06-16T08:12:05Z"
    }
  ]
}
```

---

### GET /api/claims/:id — Get Claim Detail

**Response 200:**
```json
{
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "claim_number": "CLM-2025-000001",
  "lob": "auto",
  "loss_date": "2025-06-15",
  "loss_time": "2025-06-15T14:32:00-05:00",
  "loss_location": "I-40 W near Exit 289, Raleigh, NC 27604",
  "loss_description": "Rear-end collision at highway speed...",
  "current_stage": "investigation",
  "state_jurisdiction": "NC",
  "injury_flag": true,
  "status": "open",
  "lob_metadata": {
    "vin": "1HGBH41JXMN109186",
    "make": "Honda",
    "model": "Accord",
    "year": 2021,
    "police_report_number": "RPD-2025-04471"
  },
  "insured": {
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "name": "Jane Doe",
    "email": "jane.doe@example.com",
    "phone": "+19195551234"
  },
  "assigned_adjuster": {
    "user_id": "660f9511-f30c-52e5-b827-557766551111",
    "name": "Mike Torres"
  },
  "parties": [...],
  "evidence_count": 5,
  "latest_event": {
    "event_type": "stage_change",
    "stage_to": "investigation",
    "note": "Claim received, investigation initiated.",
    "event_at": "2025-06-16T08:12:05Z"
  },
  "reported_at": "2025-06-15T19:47:22Z",
  "updated_at": "2025-06-16T08:12:05Z"
}
```

---

### PATCH /api/claims/:id — Update Claim

**Auth required.** Role: adjuster, admin.

**Request:**
```json
{
  "assigned_adjuster_id": "660f9511-f30c-52e5-b827-557766551111",
  "loss_description": "Updated description after recorded statement..."
}
```

**Response 200:** Updated claim detail object.

---

## /api/claims/:id/evidence

### POST /api/claims/:id/evidence — Upload Evidence

**Auth required.** Content-Type: `multipart/form-data`

**Form fields:**
- `file`: binary file data
- `tags`: JSON array string, e.g. `'["repair_estimate","photos_damage"]'`
- `claim_segment`: one of `bi|pd|coverage|liability|damages|general`
- `description` (optional): text note

**Response 201:**
```json
{
  "evidence_id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "original_filename": "vehicle_damage_front.jpg",
  "mime_type": "image/jpeg",
  "file_size_bytes": 3247819,
  "tags": ["photos_damage"],
  "claim_segment": "pd",
  "ocr_status": "not_applicable",
  "status": "uploaded",
  "uploaded_at": "2025-06-16T09:23:11Z",
  "presigned_view_url": "https://s3.example.com/evidence/a1b2c.../b2c3d4.jpg?X-Amz-Signature=..."
}
```

---

### GET /api/claims/:id/evidence — List Evidence

**Response 200:**
```json
{
  "total": 5,
  "evidence": [
    {
      "evidence_id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
      "original_filename": "repair_estimate.pdf",
      "mime_type": "application/pdf",
      "file_size_bytes": 812044,
      "tags": ["repair_estimate"],
      "claim_segment": "pd",
      "ocr_status": "complete",
      "ai_summary": "Repair estimate from Johnson's Auto Body. Total estimated repair cost: $8,472.00. Parts: $3,211.00, Labor: $5,261.00. Estimated completion 7–10 days. Vehicle: 2021 Honda Accord, VIN 1HGBH41JXMN109186.",
      "siu_flag": false,
      "uploaded_at": "2025-06-16T09:23:11Z"
    }
  ]
}
```

---

## /api/claims/:id/events

### POST /api/claims/:id/events — Add Event / Stage Change

**Auth required.** Role: adjuster, admin.

**Request:**
```json
{
  "event_type": "stage_change",
  "stage_to": "coverage_evaluation",
  "note": "Vehicle inspection complete. PD documented. BI claim escalated for coverage eval. Police report confirms TL fault.",
  "reserve_amount": 35000.00
}
```

**Response 201:**
```json
{
  "event_id": "c3d4e5f6-a7b8-9012-cdef-345678901234",
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "event_type": "stage_change",
  "stage_from": "investigation",
  "stage_to": "coverage_evaluation",
  "note": "Vehicle inspection complete...",
  "reserve_amount": 35000.00,
  "event_at": "2025-06-18T14:05:30Z",
  "ai_rationale_status": "queued"
}
```

---

### GET /api/claims/:id/events — Claim Timeline

**Response 200:**
```json
{
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "events": [
    {
      "event_id": "d4e5f6a7-b8c9-0123-defa-456789012345",
      "event_type": "stage_change",
      "stage_from": null,
      "stage_to": "reported",
      "note": "FNOL submitted via web portal.",
      "actor_name": "Jane Doe",
      "actor_role": "claimant",
      "event_at": "2025-06-15T19:47:22Z"
    },
    {
      "event_id": "c3d4e5f6-a7b8-9012-cdef-345678901234",
      "event_type": "stage_change",
      "stage_from": "investigation",
      "stage_to": "coverage_evaluation",
      "note": "Vehicle inspection complete...",
      "actor_name": "Mike Torres",
      "actor_role": "adjuster",
      "reserve_amount": 35000.00,
      "event_at": "2025-06-18T14:05:30Z"
    }
  ]
}
```

---

## /api/policies/explain

### POST /api/policies/explain — Policy Language Explainer

**Auth required.**

**Request:**
```json
{
  "policy_text": "SECTION II — LIABILITY COVERAGES\nCoverage E — Personal Liability\nIf a claim is made or a suit is brought against an insured for damages because of bodily injury or property damage caused by an occurrence to which this coverage applies, we will:\n1. Pay up to our limit of liability for the damages for which an insured is legally liable...",
  "lob": "homeowners",
  "policy_number_hint": "HO-003"
}
```

**Response 200:**
```json
{
  "artifact_id": "e5f6a7b8-c9d0-1234-efab-567890123456",
  "cached": false,
  "lob": "homeowners",
  "explanation": {
    "coverage_summary": "Section II provides personal liability protection, paying amounts you're legally responsible for if someone is injured or their property is damaged due to your actions or conditions on your property.",
    "what_is_covered": [
      "Bodily injury to others caused by you or household members",
      "Property damage you cause to others",
      "Legal defense costs if you're sued, even if the suit is groundless",
      "Court-ordered judgments up to the policy limit"
    ],
    "what_is_excluded": [
      "Intentional acts",
      "Business activities conducted on premises",
      "Motor vehicle liability (covered under auto policy)",
      "Liability arising from professional services"
    ],
    "key_definitions": {
      "occurrence": "An accident, including continuous or repeated exposure to substantially the same general harmful conditions.",
      "insured": "You (named insured) and residents of your household who are relatives or under age 21 in your care.",
      "bodily injury": "Bodily harm, sickness, or disease, including required care, loss of services, and death resulting therefrom."
    },
    "limits_and_deductibles": {
      "liability_limit": "See Declarations page — typically $100,000 to $500,000 per occurrence",
      "deductible": "No deductible applies to liability claims; deductible applies to property damage to your own home under Coverage A"
    },
    "citations": [
      "Coverage E definition per Section II, Coverage E paragraph 1",
      "Exclusions per Section II, Coverage E, Exclusion list items 1–12"
    ],
    "questions_to_ask_adjuster": [
      "Does my policy include an umbrella endorsement to increase the $100,000 limit?",
      "Is the incident location considered 'insured premises' under my policy?",
      "Does my policy include Coverage F — Medical Payments to Others?"
    ]
  },
  "disclaimer": "This is an informational summary only and does not constitute a legal opinion, coverage determination, or advice of counsel. For a formal coverage determination, consult your adjuster or licensed insurance professional.",
  "generated_at": "2025-06-16T10:05:00Z"
}
```

---

## /api/ai/draft-fnol

### POST /api/ai/draft-fnol — Generate FNOL Narrative

**Auth required.** Role: adjuster, admin.

**Request:**
```json
{
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

**Response 200:**
```json
{
  "artifact_id": "f6a7b8c9-d0e1-2345-fabc-678901234567",
  "claim_number": "CLM-2025-000001",
  "draft": {
    "date_of_loss": "June 15, 2025 at approximately 2:32 PM CDT",
    "insured": "Jane Doe (Policy No. [POLICY_REDACTED])",
    "loss_location": "Interstate 40 Westbound near Exit 289, Raleigh, Wake County, NC",
    "description_of_occurrence": "Insured reports that on June 15, 2025, while operating her 2021 Honda Accord (VIN: 1HGBH41JXMN109186) westbound on I-40 near Exit 289 in Raleigh, NC, she was struck from the rear by a third-party vehicle operated by John Smith. Insured states she was traveling at highway speed when the adverse driver failed to brake and collided with the rear of the insured vehicle. Airbags deployed. Insured reports neck and back discomfort. RPD responded; report number RPD-2025-04471.",
    "alleged_damages": {
      "bodily_injury": "Insured reports cervical and lumbar discomfort; transported to Rex Hospital Emergency Department for evaluation.",
      "property_damage": "Rear-end structural damage; vehicle transported from scene. Extent of damage to be assessed by reinspection."
    },
    "coverage_parts_implicated": ["Collision", "Med Pay/PIP", "Uninsured/Underinsured Motorist BI (pending adverse carrier confirmation)"],
    "next_steps": [
      "Obtain police report RPD-2025-04471",
      "Schedule vehicle inspection",
      "Confirm adverse driver's carrier and policy information",
      "Request medical authorization for BI evaluation",
      "Evaluate UM/UIM trigger upon adverse carrier confirmation"
    ]
  },
  "model_name": "gpt-4o",
  "review_required": true,
  "label": "AI Draft — Review Required Before Use",
  "generated_at": "2025-06-15T19:53:07Z"
}
```

---

## /api/ai/next-actions

### POST /api/ai/next-actions — Next-Best-Action Recommendations

**Auth required.**

**Request:**
```json
{
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "role": "adjuster"
}
```

**Response 200:**
```json
{
  "artifact_id": "a7b8c9d0-e1f2-3456-abcd-789012345678",
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "claim_number": "CLM-2025-000001",
  "role": "adjuster",
  "actions": [
    {
      "priority": "high",
      "action": "Obtain recorded statement from insured within 7 days of loss",
      "rationale": "Recorded statement required within first 10 days per best practice; loss date was June 15. NC has no statutory time limit but NAIC Model Act §901 requires prompt action.",
      "deadline": "2025-06-25",
      "action_type": "investigation",
      "link_target": "/claims/a1b2c3d4.../events/new?type=recorded_statement"
    },
    {
      "priority": "high",
      "action": "Confirm adverse driver's carrier (John Smith) and obtain DEC page",
      "rationale": "UM/UIM coverage trigger depends on adverse carrier's BI limit. If adverse limit < insured's damages, UM/UIM BI claim activates. NC UIM statute NCGS §20-279.21.",
      "deadline": "2025-06-22",
      "action_type": "coverage_investigation",
      "link_target": null
    },
    {
      "priority": "medium",
      "action": "Reserve review: evaluate adequacy of $35,000 reserve given BI component",
      "rationale": "BI reserve may be insufficient given ER visit. Hospital bills, future treatment, and wage loss exposure should be evaluated. Consider setting initial reserve at $50,000 with re-evaluation at 30 days.",
      "deadline": "2025-06-30",
      "action_type": "reserve_management",
      "link_target": "/claims/a1b2c3d4.../events/new?type=reserve_update"
    },
    {
      "priority": "low",
      "action": "Evaluate subrogation potential against John Smith / adverse carrier",
      "rationale": "Police report and insured's statement indicate third-party liability. Preserve right of subrogation per NC common law; do not release adverse party without reservation of rights.",
      "deadline": null,
      "action_type": "subrogation",
      "link_target": null
    }
  ],
  "generated_at": "2025-06-18T14:10:00Z"
}
```

---

## /api/ai/summarize

### POST /api/ai/summarize — Summarize Evidence Item

**Auth required.** Role: adjuster, admin.

**Request:**
```json
{
  "evidence_id": "b2c3d4e5-f6a7-8901-bcde-f23456789012"
}
```

**Response 200:**
```json
{
  "artifact_id": "b8c9d0e1-f2a3-4567-bcde-890123456789",
  "evidence_id": "b2c3d4e5-f6a7-8901-bcde-f23456789012",
  "summary": {
    "document_type": "Vehicle Repair Estimate",
    "issuing_party": "Johnson's Auto Body, 4400 Falls of Neuse Rd, Raleigh NC 27609",
    "key_dates": {"estimate_date": "2025-06-17", "estimated_completion": "7–10 business days"},
    "monetary_amounts": {"total_estimate": 8472.00, "parts": 3211.00, "labor": 5261.00},
    "named_parties": ["Jane Doe (customer)", "Johnson's Auto Body"],
    "vehicle": {"year": 2021, "make": "Honda", "model": "Accord", "vin": "[VIN_REDACTED_IN_LOG]"},
    "siu_indicators": [],
    "key_findings": "Repair estimate for rear-end structural damage consistent with collision description. No signs of pre-existing damage noted. Estimate from licensed NC repair facility."
  },
  "siu_flag": false,
  "model_name": "gpt-4o",
  "generated_at": "2025-06-16T09:35:00Z"
}
```

---

## /api/timelines/estimate

### POST /api/timelines/estimate — Timeline Estimation

**Auth required.**

**Request:**
```json
{
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

**Response 200:**
```json
{
  "claim_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "claim_number": "CLM-2025-000001",
  "lob": "auto",
  "state_jurisdiction": "NC",
  "injury_flag": true,
  "statute_of_limitations": {
    "bodily_injury": {"years": 3, "citation": "NCGS § 1-52(5)", "deadline_estimate": "2028-06-15"},
    "property_damage": {"years": 3, "citation": "NCGS § 1-52(5)", "deadline_estimate": "2028-06-15"}
  },
  "stage_estimates": [
    {
      "stage": "reported",
      "label": "Reported",
      "duration_business_days": {"min": 0, "max": 1},
      "rationale": "FNOL received and acknowledged same day.",
      "status": "completed",
      "entered_at": "2025-06-15"
    },
    {
      "stage": "investigation",
      "label": "Investigation",
      "duration_business_days": {"min": 5, "max": 15},
      "rationale": "Auto claims with police report typically complete investigation in 5–15 business days. Injury flag adds time for recorded statement and medical authorization.",
      "status": "in_progress",
      "entered_at": "2025-06-16",
      "estimated_exit": "2025-07-07"
    },
    {
      "stage": "coverage_evaluation",
      "label": "Coverage Evaluation",
      "duration_business_days": {"min": 3, "max": 10},
      "rationale": "Coverage eval for auto claims straightforward absent policy defenses. UM/UIM trigger confirmation may extend.",
      "status": "pending",
      "estimated_entry": "2025-07-07",
      "estimated_exit": "2025-07-21"
    },
    {
      "stage": "liability_damages",
      "label": "Liability & Damages Assessment",
      "duration_business_days": {"min": 30, "max": 90},
      "rationale": "BI claims require medical documentation, treatment completion or MMI. Expect 30–90 days depending on injury severity and treatment duration.",
      "status": "pending",
      "estimated_entry": "2025-07-21",
      "estimated_exit": "2025-10-20"
    },
    {
      "stage": "settlement_negotiation",
      "label": "Settlement Negotiation",
      "duration_business_days": {"min": 10, "max": 45},
      "rationale": "Settlement depends on demand, reserve, and negotiation complexity. Unrepresented claimants tend to settle faster.",
      "status": "pending"
    }
  ],
  "total_estimated_range": {
    "min_business_days": 48,
    "max_business_days": 161,
    "min_calendar": "2025-08-20",
    "max_calendar": "2026-01-23"
  },
  "disclaimer": "Timeline estimates are informational only and based on typical claim patterns for this LOB, jurisdiction, and complexity profile. Actual timelines vary. This is not a promise or guarantee of claim resolution.",
  "generated_at": "2025-06-18T14:15:00Z"
}
```
