# System Architecture

## Overview

Six relational databases covering the complete operational lifecycle of an
interior architecture studio — from client onboarding through project handover.

---

## Database Relationships

```
CLIENTS (1) ──────► (many) PROJECTS
PROJECTS (1) ─────► (many) RECORDS
PROJECTS (1) ─────► (many) ACTIONS
RECORDS (1) ──────► (many) ACTIONS
CONTACTS (many) ──► (many) PROJECTS   [role-based]
VENDORS (many) ───► (many) RECORDS    [assigned items]
```

---

## Record Lifecycles by Type

### Submissions
```
Received → Reference Generated → Drive Folder Created
→ Under Review → Approved / Rejected / Resubmit → Closed
```

### RFIs (5-day SLA)
```
Raised → Assigned → Under Review
→ [Day 5: SLA breach escalation triggered]
→ Responded → Acknowledged → Closed
```

### Design Change Notices / Variation Orders (DCN / VO)
```
Drafted → PM Review → Design Lead Sign-off
→ [Day 3 reminder] [Day 7 reminder] [Day 10 escalation]
→ Approved → PDF Generated → Client Notified → Acknowledged → Closed
```

### Meeting Minutes
```
Notes Submitted → AI Processing
→ Action Items Extracted → Tasks Created → Owners Notified → Tracked
```

---

## Automation Logic

### SLA Engine
- `days_open` formula calculates real-time age from submission date
- `sla_breached` flags when `days_open` exceeds threshold
- Workflows check for breaches every 6 hours and escalate automatically

### Vendor Performance Scoring
```
score = (
  (10 - avg_rfi_response_days) × 0.5 +
  (10 - quality_issues)        × 0.3 +
  compliance_rate              × 0.2
) / 10
```
Tiers: Preferred (8+) · Approved (6–8) · Probation (4–6) · Review (<4)

### Project Health Logic
- **Green** — No overdue critical items, all RFIs within SLA
- **Amber** — 1–2 overdue items or any RFI past Day 3
- **Red** — 3+ overdue critical items or any SLA breach

---

## Views by Role

| Role | Primary Views |
|---|---|
| Project Manager | Open Records · RFI Tracker · Action Board |
| Design Lead | Approval Queue · Pending Reviews · My Actions |
| Commercial | VO/DCN Pipeline · Variation Log |
| Studio Principal | Project Health Board · Weekly Dashboard |
| Client (filtered) | Their submissions and acknowledgments only |
| Vendor (filtered) | Their assigned items and response queue |
