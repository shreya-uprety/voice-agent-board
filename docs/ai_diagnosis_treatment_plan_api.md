# AI Diagnosis & AI Treatment Plan - Frontend Integration

## Quick Reference

| Field | AI Diagnosis | AI Treatment Plan |
|-------|-------------|-------------------|
| **Endpoint** | `POST /api/ai-diagnosis` | `POST /api/ai-treatment-plan` |
| **Deployed URL** | `https://voice-agent-board-481780815788.europe-west1.run.app/api/ai-diagnosis` | `https://voice-agent-board-481780815788.europe-west1.run.app/api/ai-treatment-plan` |
| **Component name** | `AIDiagnosis` | `AITreatmentPlan` |
| **Data key** | `aiDiagnosisData` | `aiTreatmentData` |
| **Zone** | `medforce-ai-diagnosis-zone` | `ai-plan-zone` |
| **Highlights** | 15-25 keywords array | 15-25 keywords array |

---

## Highlights Array

Both payloads include a `highlights` array at the root of their data object (`aiDiagnosisData.highlights` / `aiTreatmentData.highlights`). These are 15-25 clinically important keywords the frontend can use to highlight/badge important terms throughout the rendered component.

---

## Board API Response

Both endpoints return the created board item:

```json
{
  "id": "generated-item-id",
  "type": "content",
  ...
}
```

The `id` is used by the backend to auto-focus after creation.

---

## AI Diagnosis

### Endpoint

`POST /api/ai-diagnosis`

### Zone

`medforce-ai-diagnosis-zone`

### Payload

```json
{
  "title": "AI Clinical Diagnosis",
  "component": "AIDiagnosis",
  "zone": "medforce-ai-diagnosis-zone",
  "patientId": "patient-xxx",
  "aiDiagnosisData": {
    "patientInformation": {
      "name": "string",
      "mrn": "string",
      "dateOfBirth": "2024-01-15",
      "age": 58,
      "sex": "Male|Female|Other",
      "admissionDate": "2024-01-15",
      "attendingPhysician": "string"
    },
    "clinicalPresentation": {
      "chiefComplaint": "Jaundice and fatigue for 2 weeks",
      "historyOfPresentIllness": "58-year-old male presenting with progressive jaundice...",
      "reviewOfSystems": [
        {
          "system": "Gastrointestinal",
          "findings": ["Nausea", "Anorexia", "Right upper quadrant discomfort"]
        },
        {
          "system": "Constitutional",
          "findings": ["Fatigue", "Malaise", "No fever"]
        }
      ]
    },
    "diagnosticFindings": {
      "laboratory": [
        {
          "test": "ALT",
          "value": "856 U/L",
          "flag": "critical",
          "interpretation": "Markedly elevated, >20x ULN, consistent with severe hepatocellular injury"
        },
        {
          "test": "Total Bilirubin",
          "value": "8.2 mg/dL",
          "flag": "critical",
          "interpretation": "Significantly elevated, indicates impaired bilirubin conjugation"
        }
      ],
      "imaging": [
        {
          "study": "Abdominal Ultrasound",
          "findings": "Normal liver echotexture, no biliary dilatation, no focal lesions",
          "impression": "No evidence of biliary obstruction"
        }
      ]
    },
    "differentialDiagnosis": [
      {
        "rank": 1,
        "diagnosis": "Drug-Induced Liver Injury (DILI) - Amoxicillin-Clavulanate",
        "likelihood": "High",
        "supportingEvidence": [
          "Temporal relationship: symptoms 3 weeks after starting amoxicillin-clavulanate",
          "Hepatocellular pattern with R-ratio 8.2",
          "RUCAM score 7 (probable)"
        ],
        "againstEvidence": [
          "No prior rechallenge data"
        ],
        "status": "LEADING"
      },
      {
        "rank": 2,
        "diagnosis": "Autoimmune Hepatitis",
        "likelihood": "Low",
        "supportingEvidence": ["Elevated transaminases"],
        "againstEvidence": ["ANA negative", "IgG normal", "No prior autoimmune history"],
        "status": "LESS LIKELY"
      }
    ],
    "primaryDiagnosis": {
      "diagnosis": "Drug-Induced Liver Injury (DILI) secondary to Amoxicillin-Clavulanate",
      "confidence": "High",
      "clinicalReasoning": "Strong temporal relationship, hepatocellular injury pattern, RUCAM score 7 (probable), exclusion of major alternative causes",
      "icdCode": "K71.1"
    },
    "severityAssessment": {
      "overallSeverity": "SEVERE",
      "scoringSystems": [
        {
          "name": "RUCAM",
          "score": "7 (Probable)",
          "interpretation": "Probable causality between amoxicillin-clavulanate and liver injury"
        },
        {
          "name": "MELD",
          "score": "18",
          "interpretation": "Moderate severity, 3-month mortality risk ~6%"
        }
      ],
      "prognosticIndicators": [
        "Hy's Law criteria met (ALT >3x ULN + Bilirubin >2x ULN)",
        "No coagulopathy (INR 1.1)",
        "No encephalopathy"
      ]
    },
    "recommendedWorkup": [
      {
        "test": "Hepatitis A/B/C serologies",
        "rationale": "Rule out viral hepatitis",
        "urgency": "Urgent"
      },
      {
        "test": "Autoimmune panel (ANA, ASMA, IgG)",
        "rationale": "Exclude autoimmune hepatitis",
        "urgency": "Urgent"
      }
    ],
    "clinicalDecisionPoints": [
      {
        "decision": "Need for liver transplant evaluation",
        "options": ["Continue monitoring", "Refer to transplant hepatology", "Emergency listing"],
        "recommendation": "Continue monitoring with daily LFTs; refer if INR >1.5 or encephalopathy develops",
        "evidence": "EASL DILI guidelines 2019"
      }
    ],
    "highlights": [
      "DILI", "hepatocellular injury", "ALT", "RUCAM", "amoxicillin-clavulanate",
      "Hy's Law", "MELD", "bilirubin", "R-ratio", "causality assessment",
      "hepatitis serologies", "autoimmune", "INR", "encephalopathy", "transplant",
      "severity", "coagulopathy", "cholestasis", "jaundice"
    ]
  }
}
```

### Data Types Reference

| Field | Type | Values |
|-------|------|--------|
| `patientInformation.sex` | string | `Male`, `Female`, `Other` |
| `diagnosticFindings.laboratory[].flag` | string | `critical`, `high`, `normal`, `low` |
| `differentialDiagnosis[].likelihood` | string | `High`, `Moderate`, `Low` |
| `differentialDiagnosis[].status` | string | `LEADING`, `INVESTIGATING`, `LESS LIKELY`, `RULED OUT` |
| `primaryDiagnosis.confidence` | string | `High`, `Moderate`, `Low` |
| `severityAssessment.overallSeverity` | string | `MILD`, `MODERATE`, `SEVERE`, `CRITICAL` |
| `recommendedWorkup[].urgency` | string | `STAT`, `Urgent`, `Routine` |
| `highlights` | string[] | 15-25 clinically important keywords |

---

## AI Treatment Plan

### Endpoint

`POST /api/ai-treatment-plan`

### Zone

`ai-plan-zone`

### Payload

```json
{
  "title": "AI Treatment Plan",
  "component": "AITreatmentPlan",
  "zone": "ai-plan-zone",
  "patientId": "patient-xxx",
  "aiTreatmentData": {
    "patientInformation": {
      "name": "string",
      "mrn": "string",
      "dateOfBirth": "2024-01-15",
      "age": 58,
      "sex": "Male|Female|Other",
      "admissionDate": "2024-01-15",
      "attendingPhysician": "string"
    },
    "primaryDiagnosis": "Drug-Induced Liver Injury (DILI) secondary to Amoxicillin-Clavulanate",
    "treatmentObjectives": {
      "immediate": [
        "Discontinue offending agent (amoxicillin-clavulanate)",
        "Supportive care and monitoring",
        "Assess for acute liver failure criteria"
      ],
      "shortTerm": [
        "Monitor LFT trend for improvement",
        "Rule out competing diagnoses",
        "Nutritional optimization"
      ],
      "longTerm": [
        "Complete hepatic recovery",
        "Document allergy in medical record",
        "Patient education on drug avoidance"
      ]
    },
    "pharmacotherapy": {
      "currentMedications": [
        {
          "medication": "N-Acetylcysteine (NAC)",
          "dose": "70 mg/kg",
          "route": "IV",
          "frequency": "Per protocol (21-hour infusion)",
          "indication": "Hepatoprotection in non-acetaminophen DILI",
          "evidence": "EASL Grade B - shown to improve transplant-free survival in early ALF",
          "monitoring": "Monitor for anaphylactoid reactions, check ALT/AST daily"
        },
        {
          "medication": "Lactulose",
          "dose": "30 mL",
          "route": "PO",
          "frequency": "TID",
          "indication": "Prevention of hepatic encephalopathy",
          "evidence": "Standard of care for hepatic encephalopathy prophylaxis",
          "monitoring": "Stool frequency (target 2-3 soft stools/day)"
        }
      ],
      "contraindicatedMedications": [
        {
          "medication": "Amoxicillin-Clavulanate",
          "reason": "Identified causative agent of current DILI episode"
        },
        {
          "medication": "Acetaminophen",
          "reason": "Hepatotoxic risk in setting of active liver injury"
        }
      ],
      "proposedChanges": [
        {
          "action": "STOP",
          "medication": "Amoxicillin-Clavulanate 875/125 mg BID",
          "rationale": "Identified as causative agent per RUCAM assessment"
        },
        {
          "action": "START",
          "medication": "Ursodeoxycholic acid 250 mg TID",
          "rationale": "Consider if cholestatic component develops; evidence for cholestatic DILI"
        }
      ]
    },
    "nonPharmacological": [
      {
        "intervention": "Dietary modification - high-calorie, low-sodium diet",
        "frequency": "Ongoing",
        "evidence": "EASL nutritional guidelines for liver disease",
        "expectedOutcome": "Maintain nutritional status, prevent muscle wasting"
      },
      {
        "intervention": "Alcohol abstinence counseling",
        "frequency": "Once, with follow-up",
        "evidence": "Standard of care",
        "expectedOutcome": "Eliminate additional hepatotoxic exposure"
      }
    ],
    "monitoringProtocol": {
      "laboratory": [
        {
          "test": "ALT, AST, ALP, Total Bilirubin",
          "frequency": "Daily while inpatient, then twice weekly",
          "target": "Downtrend toward normalization",
          "escalation": "If ALT rises >50% from nadir or new peak, escalate to hepatology"
        },
        {
          "test": "INR, PT",
          "frequency": "Daily",
          "target": "INR <1.5",
          "escalation": "If INR >1.5, consider FFP and transplant evaluation"
        },
        {
          "test": "Creatinine, BUN",
          "frequency": "Daily",
          "target": "Within normal limits",
          "escalation": "If creatinine >1.5 mg/dL, evaluate for hepatorenal syndrome"
        }
      ],
      "clinical": [
        {
          "parameter": "Mental status / Hepatic encephalopathy grading",
          "frequency": "Every shift (q8h)",
          "escalation": "Any grade of encephalopathy: STAT hepatology consult, consider ICU transfer"
        },
        {
          "parameter": "Abdominal exam (ascites, tenderness)",
          "frequency": "Daily",
          "escalation": "New ascites: diagnostic paracentesis, albumin infusion"
        }
      ]
    },
    "escalationPathway": [
      {
        "trigger": "INR >1.5 or any encephalopathy",
        "action": "STAT hepatology consult, evaluate for transplant listing",
        "urgency": "IMMEDIATE",
        "contactTeam": "Transplant Hepatology"
      },
      {
        "trigger": "Creatinine >1.5 mg/dL with oliguria",
        "action": "Evaluate for hepatorenal syndrome, start albumin challenge",
        "urgency": "URGENT",
        "contactTeam": "Nephrology"
      },
      {
        "trigger": "Hemodynamic instability or GI bleeding",
        "action": "ICU transfer, crossmatch blood products",
        "urgency": "IMMEDIATE",
        "contactTeam": "Critical Care / GI"
      }
    ],
    "consultations": [
      {
        "specialty": "Hepatology",
        "reason": "Severe DILI management, transplant evaluation if deterioration",
        "urgency": "Urgent",
        "status": "PENDING"
      },
      {
        "specialty": "Clinical Pharmacology",
        "reason": "Causality assessment review, alternative antibiotic selection",
        "urgency": "Routine",
        "status": "PENDING"
      }
    ],
    "prognosticOutlook": {
      "expectedCourse": "Most amoxicillin-clavulanate DILI cases resolve within 4-8 weeks after drug withdrawal. Cholestatic forms may take up to 6 months.",
      "riskFactors": [
        "Hy's Law criteria met",
        "Age >55",
        "Pre-existing liver disease"
      ],
      "followUpSchedule": "Weekly LFTs for 4 weeks post-discharge, then biweekly until normalization. Hepatology follow-up at 2 and 6 weeks."
    },
    "highlights": [
      "N-acetylcysteine", "hepatoprotection", "DILI", "drug withdrawal",
      "MELD score", "INR monitoring", "transplant evaluation", "lactulose",
      "encephalopathy", "hepatorenal syndrome", "RUCAM", "ursodeoxycholic acid",
      "coagulopathy", "amoxicillin-clavulanate", "escalation", "LFT trend",
      "albumin", "hepatology consult", "Hy's Law"
    ]
  }
}
```

### Data Types Reference

| Field | Type | Values |
|-------|------|--------|
| `patientInformation.sex` | string | `Male`, `Female`, `Other` |
| `treatmentObjectives.immediate` | string[] | Immediate goals (first 24-48h) |
| `treatmentObjectives.shortTerm` | string[] | Short-term goals (1-4 weeks) |
| `treatmentObjectives.longTerm` | string[] | Long-term goals (months) |
| `pharmacotherapy.currentMedications[].route` | string | `PO`, `IV`, `IM`, `SC`, etc. |
| `pharmacotherapy.proposedChanges[].action` | string | `START`, `STOP`, `ADJUST`, `SWITCH` |
| `monitoringProtocol.laboratory[].frequency` | string | e.g. `Daily`, `Twice weekly`, `Weekly` |
| `escalationPathway[].urgency` | string | `IMMEDIATE`, `URGENT`, `ROUTINE` |
| `consultations[].urgency` | string | `STAT`, `Urgent`, `Routine` |
| `consultations[].status` | string | `PENDING`, `REQUESTED`, `COMPLETED` |
| `highlights` | string[] | 15-25 clinically important keywords |
