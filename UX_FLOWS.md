# UX Flows — Insurance Claim Assistant

**Version:** 1.0  
**Date:** 2025-01-01

All flows use mobile-first responsive design. Breakpoints: mobile (375px), tablet (768px), desktop (1280px).

---

## 1. Dashboard

```
┌──────────────────────────────────────────────────────────────────────┐
│  NAVBAR: [🛡 ClaimAssist]              [My Claims] [Policy] [Profile] │
├──────────────────────────────────────────────────────────────────────┤
│  Welcome back, Jane.                              [+ File New Claim] │
├──────────────────────────────────────────────────────────────────────┤
│  MY CLAIMS                                                           │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ CLM-2025-000001                            [AUTO] [OPEN]         │ │
│  │ Loss Date: Jun 15, 2025 · Raleigh, NC                           │ │
│  │ Stage: ●●●○○○  Investigation (Day 3)                            │ │
│  │ Assigned: Mike Torres · BI/PD claim                             │ │
│  │ [View Claim →]                                  [Upload Evidence]│ │
│  └─────────────────────────────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │ CLM-2024-000788                          [HOME] [CLOSED]         │ │
│  │ Loss Date: Nov 2, 2024 · Charlotte, NC                          │ │
│  │ Stage: ●●●●●●  Closed (Settled)                                 │ │
│  │ [View Claim →]                                                   │ │
│  └─────────────────────────────────────────────────────────────────┘ │
│                                                                      │
│  QUICK ACTIONS                                                       │
│  [🔍 Explain My Policy]  [📄 Upload Document]  [📊 Check Timeline]   │
└──────────────────────────────────────────────────────────────────────┘
```

**Adjuster Dashboard (role=adjuster):**
```
┌──────────────────────────────────────────────────────────────────────┐
│  NAVBAR: [🛡 ClaimAssist]    [Queue] [Reports] [Search] [Profile]    │
├──────────────────────────────────────────────────────────────────────┤
│  CLAIM QUEUE  · 24 Open Claims                    [Filters ▼] [Sort]│
│  ┌───────────────────────────────────────────────────────────────────┤
│  │ ⚠ HIGH PRIORITY (3)                                              │
│  │  CLM-2025-000001  AUTO/BI  Jane Doe  Rpt: Jun 15  Stage: Invest  │
│  │  [AI FNOL Draft Ready] [2 evidence items awaiting review]        │
│  │─────────────────────────────────────────────────────────────────│
│  │ STANDARD (21)                                                    │
│  │  CLM-2025-000003  HOME/Water  Mark Lee  Rpt: Jun 14  Stage: Rpt  │
│  └──────────────────────────────────────────────────────────────────┘
│  KPI BAR: Open: 24 | Avg Days Open: 18.3 | BI Claims: 7 | SIU: 1   │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 2. New Claim Wizard (FNOL Intake)

**URL:** `/claims/new`  
**Steps:** LOB → Loss Details → Parties → Review & Submit → Confirmation

### Step 1: Select Line of Business
```
┌──────────────────────────────────────────────────────────────────────┐
│  File a New Claim                                 Step 1 of 5 ●○○○○ │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  What type of insurance claim are you filing?                        │
│                                                                      │
│  ┌────────────────┐  ┌────────────────┐  ┌────────────────────────┐ │
│  │                │  │                │  │                        │ │
│  │   🚗 AUTO      │  │  🏠 HOME       │  │  🏢 GENERAL LIABILITY  │ │
│  │                │  │                │  │                        │ │
│  │ Vehicle damage │  │ Property loss  │  │  Business liability    │ │
│  │ or injuries    │  │ or damage      │  │  or bodily injury      │ │
│  │                │  │                │  │                        │ │
│  └────────────────┘  └────────────────┘  └────────────────────────┘ │
│                                                                      │
│  Need help choosing?  [What type is my claim? →]                    │
│                                                                      │
│                                              [Cancel]  [Next →]     │
└──────────────────────────────────────────────────────────────────────┘
```

---

### Step 2a: Loss Details — AUTO
```
┌──────────────────────────────────────────────────────────────────────┐
│  File a New Claim — Auto                          Step 2 of 5 ●●○○○ │
├──────────────────────────────────────────────────────────────────────┤
│  LOSS INFORMATION                                                    │
│                                                                      │
│  Date of Loss *                                                      │
│  [06/15/2025        ▼]                                              │
│                                                                      │
│  Approximate Time of Loss                                            │
│  [2:32 PM           ▼]                                              │
│                                                                      │
│  State Where Loss Occurred *                                         │
│  [North Carolina    ▼]                                              │
│                                                                      │
│  Location of Loss (street address or description) *                 │
│  [I-40 W near Exit 289, Raleigh, NC                              ] │
│                                                                      │
│  VEHICLE INFORMATION                                                 │
│                                                                      │
│  VIN  [1HGBH41JXMN109186                                        ] │
│       (Enter VIN to auto-populate year/make/model)                  │
│  Year  [2021] Make  [Honda         ] Model [Accord              ]   │
│  License Plate  [ABC-1234    ] State  [NC ▼]                       │
│                                                                      │
│  LOSS TYPE (check all that apply)                                   │
│  ☑ Property Damage (PD)     ☑ Bodily Injury (BI)                   │
│  ☐ Theft/Vandalism          ☐ Glass Only                           │
│                                                                      │
│  ⚠ You indicated Bodily Injury. You will be asked to provide       │
│    injury details and medical authorization in a later step.        │
│                                                                      │
│  Was a police report filed?  ● Yes  ○ No  ○ Unknown               │
│  Police Report Number  [RPD-2025-04471                          ]   │
│                                                                      │
│  Describe what happened *                                            │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │ I was traveling westbound on I-40 when the car behind me... │    │
│  └─────────────────────────────────────────────────────────────┘    │
│  [                                                    20/2000 chars] │
│                                                                      │
│                              [← Back]               [Next →]       │
└──────────────────────────────────────────────────────────────────────┘
```

---

### Step 2b: Loss Details — HOMEOWNERS
```
┌──────────────────────────────────────────────────────────────────────┐
│  File a New Claim — Homeowners                    Step 2 of 5 ●●○○○ │
├──────────────────────────────────────────────────────────────────────┤
│  LOSS INFORMATION                                                    │
│                                                                      │
│  Date of Loss *              Date Discovered (if different)          │
│  [11/02/2024    ▼]           [11/03/2024    ▼]                      │
│                                                                      │
│  Type of Peril *                                                     │
│  ○ Fire/Smoke   ○ Water/Flooding   ● Wind/Hail   ○ Theft            │
│  ○ Vandalism    ○ Collapse         ○ Other: [____________]          │
│                                                                      │
│  Property Address *                                                  │
│  [123 Maple St, Charlotte, NC 28205                              ]  │
│                                                                      │
│  Occupancy Type *                                                    │
│  ● Owner-Occupied  ○ Tenant-Occupied  ○ Vacant  ○ Secondary Home   │
│                                                                      │
│  Areas Affected (check all)                                         │
│  ☑ Roof    ☑ Exterior    ☐ Kitchen    ☐ Basement    ☐ Other         │
│                                                                      │
│  Estimated Damage Amount (your estimate)                            │
│  [$  15,000                                                     ]   │
│                                                                      │
│  Is there a mortgage on this property?  ● Yes  ○ No                │
│  Mortgagee Name  [Wells Fargo Bank NA                           ]   │
│  Loan Number    [WF-4521887                                     ]   │
│                                                                      │
│  Is property inhabitable?  ● Yes  ○ No — Loss of Use may apply     │
│                                                                      │
│  Describe what happened *                                            │
│  [                                                             ]    │
│                                                                      │
│                              [← Back]               [Next →]       │
└──────────────────────────────────────────────────────────────────────┘
```

---

### Step 3: Party Information
```
┌──────────────────────────────────────────────────────────────────────┐
│  File a New Claim                                 Step 3 of 5 ●●●○○ │
│  Parties Involved                                                    │
├──────────────────────────────────────────────────────────────────────┤
│  YOUR INFORMATION (Insured)                                          │
│  First Name [Jane          ] Last Name [Doe             ]           │
│  Phone  [+1 (919) 555-1234 ]  Email [jane.doe@email.com ]          │
│  Address [456 Oak Ave, Raleigh, NC 27603                       ]    │
│                                                                      │
│  ARE THERE OTHER PARTIES INVOLVED?  ● Yes  ○ No                    │
│                                                                      │
│  ┌─ OTHER PARTY 1 ──────────────────────────────────────────────┐   │
│  │ Party Type  [Third Party — Driver   ▼]                        │   │
│  │ Name  [John              ] [Smith          ]                  │   │
│  │ Phone [+1 (919) 555-9876 ]                                    │   │
│  │ Insurance Company  [State Auto Ins.        ]                  │   │
│  │ Policy Number  [SA-2024-889900             ]                  │   │
│  │                                          [Remove Party]       │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  [+ Add Another Party]                                              │
│                                                                      │
│  Are any witnesses available?  ○ Yes  ● No                         │
│                                                                      │
│                              [← Back]               [Next →]       │
└──────────────────────────────────────────────────────────────────────┘
```

---

### Step 4: Review & Submit
```
┌──────────────────────────────────────────────────────────────────────┐
│  Review Your Claim                                Step 4 of 5 ●●●●○ │
├──────────────────────────────────────────────────────────────────────┤
│  Please review the information below before submitting.             │
│                                                                      │
│  LOSS SUMMARY                                         [Edit]        │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ LOB: Auto                   Date of Loss: Jun 15, 2025        │  │
│  │ Location: I-40 W Exit 289, Raleigh, NC                        │  │
│  │ Vehicle: 2021 Honda Accord  VIN: 1HGBH41JXMN109186            │  │
│  │ Police Report: RPD-2025-04471                                  │  │
│  │ Loss Types: Property Damage (PD) + Bodily Injury (BI)         │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  PARTIES                                              [Edit]        │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ Insured: Jane Doe · (919) 555-1234                            │  │
│  │ Third Party: John Smith · Adverse driver                      │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ ☑ I certify that the information provided is accurate to       │  │
│  │   the best of my knowledge. I understand that providing        │  │
│  │   false information may be considered insurance fraud.         │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  By submitting, you agree to our Terms of Service and               │
│  Privacy Policy.                                                    │
│                                                                      │
│                       [← Back]        [Submit Claim →]             │
└──────────────────────────────────────────────────────────────────────┘
```

---

### Step 5: Confirmation
```
┌──────────────────────────────────────────────────────────────────────┐
│                          ✓ Claim Filed                              │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Your claim has been submitted successfully.                        │
│                                                                      │
│  CLAIM NUMBER                                                        │
│  ┌──────────────────────────────────────────────────────────┐       │
│  │              CLM-2025-000001                             │       │
│  │              Save or screenshot this number.             │       │
│  └──────────────────────────────────────────────────────────┘       │
│                                                                      │
│  What happens next:                                                  │
│  ① A confirmation email was sent to jane.doe@email.com              │
│  ② An adjuster will be assigned within 1 business day               │
│  ③ You may be contacted to provide a recorded statement             │
│  ④ Upload evidence (photos, police report) to speed up your claim   │
│                                                                      │
│  [View My Claim →]              [Upload Evidence Now →]             │
│                                                                      │
│  Questions? Call 1-800-CLAIMS-1 or chat with our team.             │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 3. Claim Detail — Tabbed View

**URL:** `/claims/[id]`  
**Tabs:** Overview | Evidence | Timeline | Policy | AI Insights

### Header (all tabs)
```
┌──────────────────────────────────────────────────────────────────────┐
│  ← Back to Dashboard                                                 │
│                                                                      │
│  CLM-2025-000001                            [AUTO] [OPEN]  [⋮ Menu] │
│  Loss: Jun 15, 2025 · I-40 W, Raleigh, NC                          │
│  Adjuster: Mike Torres · mike.torres@carrier.com                    │
│                                                                      │
│  STATUS: ●●●○○○  Investigation                                      │
│  Day 3 of Investigation · Est. completion: Jul 7, 2025             │
│                                                                      │
│  [Overview] [Evidence (5)] [Timeline] [Policy] [AI Insights]        │
└──────────────────────────────────────────────────────────────────────┘
```

### Tab: Overview
```
├──────────────────────────────────────────────────────────────────────┤
│  CLAIM DETAILS                          NEXT ACTIONS (AI)           │
│  LOB:     Auto                          ┌────────────────────────┐  │
│  Loss:    Jun 15, 2025 at 2:32 PM       │ ⚠ Upload police report │  │
│  Location: I-40 W Exit 289, Raleigh NC  │ ○ Provide medical auth │  │
│  State:   NC                            │ ○ Recorded statement   │  │
│  BI Flag: Yes ⚠                        │  due Jun 25            │  │
│  PD Flag: Yes                           │ [View All Actions →]   │  │
│                                         └────────────────────────┘  │
│  VEHICLE                                                             │
│  2021 Honda Accord                                                   │
│  VIN: 1HGBH41JXMN109186                                            │
│  Police Report: RPD-2025-04471                                      │
│                                                                      │
│  PARTIES                                                             │
│  Insured: Jane Doe                                                   │
│  Adverse: John Smith (Third-Party Driver)                           │
│                                                                      │
│  RESERVE (Adjuster View Only)                                        │
│  Current Reserve: $35,000.00      [Update Reserve]                  │
└──────────────────────────────────────────────────────────────────────┘
```

### Tab: Evidence
```
├──────────────────────────────────────────────────────────────────────┤
│  EVIDENCE ITEMS (5)                          [+ Upload Evidence]    │
│  Filter: [All Segments ▼] [All Tags ▼] [All Statuses ▼]           │
│                                                                      │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ 📄 repair_estimate.pdf                    PD · Repair Estimate │  │
│  │ Uploaded Jun 16 · 812 KB · OCR ✓ · AI Summary ✓              │  │
│  │ "Total estimate: $8,472.00. Parts: $3,211, Labor: $5,261..."  │  │
│  │ [View] [AI Summary] [Download]                    SIU: Clear  │  │
│  └───────────────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ 📷 vehicle_damage_front.jpg                PD · Photos: Damage│  │
│  │ Uploaded Jun 16 · 3.2 MB · OCR: N/A                          │  │
│  │ [View] [Download]                                             │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                      │
│  UPLOAD EVIDENCE                                                     │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │   Drag & drop files here, or click to browse                 │   │
│  │   Accepted: JPEG, PNG, PDF, MP4, MOV, DOCX · Max 50MB       │   │
│  └──────────────────────────────────────────────────────────────┘   │
│  Tag: [photos_damage ▼]  Segment: [Property Damage ▼]              │
└──────────────────────────────────────────────────────────────────────┘
```

### Tab: Timeline
```
├──────────────────────────────────────────────────────────────────────┤
│  CLAIM TIMELINE                                                      │
│                                                                      │
│  ●─────────────────────────────────────────────────────────────     │
│  ✓ REPORTED                    Jun 15, 2025                         │
│    FNOL submitted via portal.  Day 0                                │
│                                                                      │
│  ●─────────────────────────────────────────────────────────────     │
│  ◉ INVESTIGATION               Jun 16, 2025 → Est. Jul 7          │
│    Adjuster assigned. Vehicle inspection scheduled.  Day 3          │
│    [View AI Rationale]                                              │
│                                                                      │
│  ○─────────────────────────────────────────────────────────────     │
│  ○ COVERAGE EVALUATION         Est. Jul 7 → Jul 21                 │
│                                                                      │
│  ○─────────────────────────────────────────────────────────────     │
│  ○ LIABILITY & DAMAGES         Est. Jul 21 → Oct 20                │
│    ⚠ BI component adds 30–90 days for medical documentation        │
│                                                                      │
│  ○─────────────────────────────────────────────────────────────     │
│  ○ SETTLEMENT NEGOTIATION      Est. Oct 20 → Dec 2025             │
│                                                                      │
│  ○─────────────────────────────────────────────────────────────     │
│  ○ CLOSED                                                           │
│                                                                      │
│  NC Statute of Limitations: BI = 3 years (Jun 15, 2028)            │
│  per NCGS § 1-52(5)                                                 │
└──────────────────────────────────────────────────────────────────────┘
```

### Tab: AI Insights (Adjuster View)
```
├──────────────────────────────────────────────────────────────────────┤
│  AI INSIGHTS                        Last updated: Jun 18, 2025      │
│                                                                      │
│  FNOL DRAFT                                        [Regenerate]     │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │ ⚠ AI Draft — Review Required                                 │   │
│  │                                                              │   │
│  │ Date of Loss: June 15, 2025 at approximately 2:32 PM CDT.    │   │
│  │ Insured reports that while operating her 2021 Honda Accord   │   │
│  │ westbound on I-40 near Exit 289, Raleigh, NC, she was struck │   │
│  │ from the rear by John Smith. Airbags deployed. Insured        │   │
│  │ transported to Rex Hospital for evaluation of neck and back. │   │
│  │                                                              │   │
│  │ [Accept Draft]  [Edit Draft]  [Reject Draft]                 │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  NEXT ACTIONS (4)                                                    │
│  [⚠ HIGH] Recorded statement due Jun 25                            │
│  [⚠ HIGH] Confirm adverse carrier & policy limits by Jun 22       │
│  [MED] Reserve review: consider increasing to $50,000             │
│  [LOW] Evaluate subrogation against adverse driver                 │
│                                                                      │
│  SIU INDICATORS: None detected                                      │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 4. Policy Explainer

**URL:** `/policy-explainer`

```
┌──────────────────────────────────────────────────────────────────────┐
│  NAVBAR: [🛡 ClaimAssist]              [My Claims] [Policy] [Profile] │
├──────────────────────────────────────────────────────────────────────┤
│  Policy Language Explainer                                          │
│  Paste any section of your insurance policy for a plain-English      │
│  breakdown.                                                         │
├──────────────────────────────────────────────────────────────────────┤
│  Line of Business  [Auto ▼]                                         │
│                                                                      │
│  Policy Text                                                         │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │ Paste your policy language here...                           │   │
│  │                                                              │   │
│  │                                                              │   │
│  │                                                              │   │
│  │                                        0 / 10,000 characters │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  ⓘ Do not include your SSN, policy number, or personal details.    │
│    Personal information is redacted before analysis.               │
│                                                                      │
│                               [Explain This Language →]            │
├──────────────────────────────────────────────────────────────────────┤
│  RESULT                                                              │
│                                                                      │
│  COVERAGE SUMMARY                                                    │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │ Comprehensive coverage pays for damage to your vehicle       │   │
│  │ caused by events other than a collision...                   │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  WHAT'S COVERED               WHAT'S EXCLUDED                       │
│  ✓ Theft                      ✗ Collision damage                    │
│  ✓ Fire                       ✗ Mechanical breakdown                │
│  ✓ Weather/Hail               ✗ Wear and tear                       │
│                                                                      │
│  KEY DEFINITIONS              LIMITS & DEDUCTIBLES                  │
│  covered auto: [...]          Your deductible: per Declarations      │
│                                                                      │
│  QUESTIONS TO ASK YOUR ADJUSTER                                     │
│  → Does my policy include rental reimbursement?                    │
│  → Is my deductible waived for UM/UIM comprehensive losses?        │
│                                                                      │
│  ⚠ This is an informational explanation only. Not a coverage       │
│    determination. Consult your adjuster for formal coverage review. │
│                                                                      │
│  [Copy Explanation]  [Ask Another Question]                         │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 5. Evidence Uploader (Modal / Drawer)

```
┌──────────────────────────────────────────────────────────────────────┐
│  Upload Evidence                                              [✕]   │
│  Claim: CLM-2025-000001                                             │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │                                                              │   │
│  │              📁 Drag files here                             │   │
│  │            or click to browse                               │   │
│  │                                                              │   │
│  │    JPEG · PNG · HEIC · PDF · MP4 · MOV · DOCX  (Max 50MB)  │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  Claim Segment *                                                     │
│  ○ Bodily Injury (BI)  ● Property Damage (PD)  ○ Coverage           │
│  ○ Liability           ○ Damages               ○ General            │
│                                                                      │
│  Document Type *                                                     │
│  [Repair Estimate                               ▼]                  │
│  Options: Police Report | Medical Record | Repair Estimate |        │
│           Photos - Damage | Photos - Scene | Recorded Statement |   │
│           Coverage Document | Other                                 │
│                                                                      │
│  Note (optional)                                                     │
│  [From Johnson's Auto Body, received Jun 16                    ]    │
│                                                                      │
│  SELECTED FILES                                                      │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │ 📄 repair_estimate.pdf      812 KB     ✓ Ready to upload    │   │
│  └──────────────────────────────────────────────────────────────┘   │
│                                                                      │
│                          [Cancel]      [Upload Files →]            │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 6. Settings

**URL:** `/settings`

```
┌──────────────────────────────────────────────────────────────────────┐
│  Settings                                                           │
├──────────────────────────────────────────────────────────────────────┤
│  PROFILE                                                             │
│  First Name  [Jane          ]  Last Name  [Doe          ]           │
│  Email  [jane.doe@example.com                          ]            │
│  Phone  [+1 (919) 555-1234                             ]            │
│  [Save Profile]                                                     │
├──────────────────────────────────────────────────────────────────────┤
│  NOTIFICATIONS                                                       │
│  ☑ Email me when my claim stage changes                            │
│  ☑ Email me when evidence processing completes                     │
│  ☐ SMS notifications                                               │
│  [Save Notifications]                                               │
├──────────────────────────────────────────────────────────────────────┤
│  SECURITY                                                            │
│  [Change Password]                                                  │
│  [Download My Data]   [Delete My Account]                          │
└──────────────────────────────────────────────────────────────────────┘
```

---

## 7. Navigation States & Error States

### Loading State
```
┌────────────────────────────────────┐
│  Loading your claim...             │
│  ████████████░░░░  Processing      │
└────────────────────────────────────┘
```

### Empty State (No Claims)
```
┌──────────────────────────────────────────────────────────────────────┐
│                                                                      │
│                    🛡                                                │
│           No claims on file                                         │
│                                                                      │
│   When you file a claim, it will appear here.                       │
│   Most claims are processed within 30 days.                         │
│                                                                      │
│               [+ File Your First Claim]                             │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

### Validation Error
```
  Date of Loss *
  [06/20/2025    ▼]
  ⚠ Date of loss cannot be in the future.
```

### AI Unavailable State
```
┌────────────────────────────────────────────────────────────┐
│ ⚠ AI features temporarily unavailable                     │
│ Your claim data is saved. AI features will be available   │
│ when service is restored. Estimated: ~15 minutes.          │
│ [Try Again]                                               │
└────────────────────────────────────────────────────────────┘
```
