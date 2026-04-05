# Report Format Template

The final report must be written in the same language as the input specification. English input = English report. Russian input = Russian report. Only verdict keywords (READY/NEEDS_WORK/NOT_READY) and summary.json keys remain in English always.

## Required Sections

### 1. Executive Summary
2-3 sentences maximum. What was reviewed, the key risk, and the verdict. This is a judgment for a non-technical stakeholder, not a feature list or methodology description.

### 2. Verdict
One of: **READY** / **NEEDS_WORK** / **NOT_READY** (always English keyword)

Decision logic:
- **READY**: 0 blockers, 0 dead-end states, fewer than 3 high-severity findings
- **NEEDS_WORK**: 0 blockers, but dead-end states present or 3+ high-severity findings
- **NOT_READY**: any blocker findings present

If Validator found 0 issues in round 1: include warning "Zero issues found is suspicious per cognitive rule #4."

Verdict must be justified by the findings. It must not contradict the evidence.

### 3. Findings by Severity

Group by severity: BLOCKER first, then HIGH, MEDIUM, LOW.

Each finding:
```
### [SEVERITY] F-[N]: [Title]
Category: [dead-end / open question / role conflict / timing collision / escalation break / missing area / scenario gap]
Impacted scenarios: [list]
Spec reference: [section or "spec is silent"]

[Description: what's wrong, why it matters, what breaks in production]

Proposed options:
  A) [option with trade-off]
  B) [option with trade-off]
  C) [option with trade-off]
```

### 4. Dead-End States

For each dead-end:
- Which state the entity gets stuck in
- Why it gets stuck (what action/timeout/escalation is missing)
- Real-world consequence (not abstract — who suffers and how)
- Which scenarios lead to this state

### 5. Open Questions

For each question:
- Specific situation a developer would face
- Which scenarios are impacted
- 2-3 realistic options (not academic)
- Severity rating

### 6. Decision Log (Builder-Validator Debate)

For each round:
```
## Round N

**Builder proposed:** [summary of key scenarios and positions]
**Validator attacked:** [summary of key findings]
**Accepted:** [which findings were addressed in next round]
**Rejected/Deferred:** [which findings were not addressed and why]
**Key decisions:** [any trade-offs made]
```

This section is required for Pipeline v3 Gate 4: CTO must be able to read the debate log and make GO/NO GO decision in 10 minutes.

### 7. State Transitions

Text table format — no ASCII art diagrams:
```
| From | To | Trigger | Condition |
|------|-----|---------|-----------|
```

Include dead-end analysis below the table.

### 8. BDD Scenarios (Final)

All scenarios from the final round, grouped by feature area. Include confidence tags (SOLID/INFERRED/GAP), spec references, MUST NOT sections, and EDGE CASES.

### 9. Coverage Map

```
| Feature Area | Scenarios | Coverage |
|-------------|-----------|----------|
```

Honestly flag zero-coverage areas. Check standard enterprise areas: search, reporting, audit trail, archiving, user management, notifications, access control, error handling.

### 10. Glossary

Domain terms with definitions that help a new developer understand the domain. Not spec verbatim — add boundaries and clarify what the term does NOT include.

### 11. Round Progression

Show round-by-round delta:
```
| Round | Scenarios | New Findings | Resolved | Delta |
|-------|-----------|-------------|----------|-------|
```

Indicate whether the process is converging (fewer new findings each round) or diverging (more findings each round).

## summary.json Schema

```json
{
  "spec_source": "string — filename or 'inline prompt'",
  "spec_words": "number",
  "language": "string — 'en', 'ru', etc.",
  "domain": "string — domain name or 'generic'",
  "mode": "string — 'fast' or 'strict'",
  "rounds_completed": "number",
  "scenarios_total": "number",
  "scenarios_solid": "number",
  "scenarios_inferred": "number",
  "scenarios_gap": "number",
  "open_questions": "number",
  "dead_ends": "number",
  "findings_blocker": "number",
  "findings_high": "number",
  "findings_medium": "number",
  "findings_low": "number",
  "coverage_gaps": ["array of uncovered area names"],
  "verdict": "READY | NEEDS_WORK | NOT_READY"
}
```

All keys in English. Values in the spec's language where they describe domain content. Numbers must match the report content exactly.
