---
name: builder-validator
description: Run Builder-Validator cycles on requirements to find gaps, dead-end states, unanswered questions, and missing functional areas before writing code. Input can be a spec document (PDF, DOCX, MD), a free-form prompt with ideas, screenshots, links, or any mix. Builder creates BDD scenarios and state machines, Validator adversarially attacks them. Use when the user asks to "review a spec", "review my spec", "find gaps", "attack my spec", "BDD from spec", "BDD from this idea", "validate requirements", "analyze requirements", "spec review", "create scenarios from this", or wants to find problems before building. Works on any domain.
argument-hint: <spec-file-or-prompt> [--domain banking|telecom|government|healthcare] [--rounds 4] [--mode fast|strict]
---

# Builder-Validator Spec Review

Runs adversarial Builder-Validator cycles on requirements to find gaps before code is written. Based on EvoGroup Pipeline v3, Step 2.

## How It Works

1. **Builder** reads the requirements and creates BDD scenarios with MUST NOT sections, state machines, and coverage maps
2. **Validator** attacks Builder's output from a fresh perspective — finds dead-ends, open questions, role conflicts, timing collisions, and missing areas
3. Repeat for N rounds. Each round, Builder addresses Validator's findings.
4. Final report with verdict: READY / NEEDS_WORK / NOT_READY

## Execution Flow

### Step 1: Parse Input

Parse `$ARGUMENTS` to determine:
- **Input source**: file path, inline text, or mixed
- **Flags**: `--domain`, `--rounds` (default: 4, max: 8), `--mode` (default: fast)

### Step 2: Content Inventory

Before any analysis, understand what we're working with:

**For file input:**
- `.md`, `.txt` — Read directly, count words
- `.pdf` — Run `pdfinfo` via Bash to get page count and metadata. Sample first page to check text extractability. If scanned (no text), warn user and suggest OCR'd version. Read with pagination: `pages: "1-20"`, `"21-40"`, etc. (20 pages per Read call)
- `.docx` — Try conversion chain:
  1. `pandoc "$FILE" -t markdown` (primary)
  2. `python3 -c "from docx import Document; ..."` (fallback)
  3. `python3 -c "import zipfile; ..."` to extract word/document.xml (last resort)
  4. If all fail: tell user honestly, suggest converting to PDF or MD
- Other formats: tell user which formats are supported

**For inline prompt:** use the text directly as requirements input.

**For screenshots/images:** Read tool supports images natively. Extract visible requirements, UI flows, diagrams.

**For links:** use WebFetch if available, otherwise note as external reference the user should paste.

Save extracted content to `.spec-review/input/spec.md`.

### Step 3: Detect Language

Read the first ~500 words of the input. Detect language (Russian, English, or other).

**Language determines BDD format:**
- English → Given/When/Then, MUST NOT, BOUNDARY VALUES, EDGE CASES
- Russian → ДАНО/КОГДА/ТОГДА, НЕ ДОЛЖНО, ГРАНИЧНЫЕ ЗНАЧЕНИЯ

Report to user: input type, word count, language detected, estimated token cost (~300 tokens/page).

### Step 4: Configuration

If `--domain` not specified, ask user: "What domain? (banking / telecom / government / healthcare / skip)"

If domain selected, read domain patterns from `${CLAUDE_SKILL_DIR}/references/domains/{domain}.md`.

Read cognitive rules from `${CLAUDE_SKILL_DIR}/references/cognitive-rules.md`.

### Step 5: Create Working Directory

```
mkdir -p .spec-review/rounds .spec-review/output
```

### Step 6: Round Loop

For each round N from 1 to max_rounds:

#### 6a: Builder Phase

**Fast mode:** Read `${CLAUDE_PLUGIN_ROOT}/agents/builder.md` and adopt the Builder role. Read the spec from `.spec-review/input/spec.md`. If round > 1, also read previous Validator findings from `.spec-review/rounds/round-{N-1}/validator-findings.md`. Generate all Builder artifacts and write to `.spec-review/rounds/round-{N}/`.

**Strict mode:** Spawn a fresh subagent with `subagent_type: "general-purpose"`. Provide in the prompt:
- Full content of `${CLAUDE_PLUGIN_ROOT}/agents/builder.md`
- Spec text from `.spec-review/input/spec.md`
- Cognitive rules from `${CLAUDE_SKILL_DIR}/references/cognitive-rules.md`
- Domain patterns (if selected)
- Previous Validator findings path (if round > 1)
- Output directory: `.spec-review/rounds/round-{N}/`
- Detected language

Wait for the subagent to complete. Verify Builder wrote all 4 files: `builder-scenarios.md`, `builder-states.md`, `builder-glossary.md`, `builder-coverage.md`.

#### 6b: MUST NOT Validation Gate

After Builder writes artifacts, check `builder-scenarios.md`:
- Does it contain MUST NOT sections (English) or НЕ ДОЛЖНО sections (Russian)?
- If missing: in Fast mode, instruct Builder to add them. In Strict mode, re-spawn Builder with explicit instruction: "Your scenarios are missing MUST NOT sections. Per Pipeline v3, without MUST NOT the scenario is NOT ready. Add MUST NOT sections to every scenario group."

This gate prevents non-compliant Builder output from reaching Validator.

#### 6c: Validator Phase

**Fast mode:** Read `${CLAUDE_PLUGIN_ROOT}/agents/validator.md` and adopt the Validator role. Read Builder artifacts FRESH from disk (not from memory). Read the original spec again. Attack.

Key instruction for Fast mode: "You are now Validator. Forget everything about how the Builder created these scenarios. You see ONLY the artifacts and the spec. You have no knowledge of the Builder's reasoning. Attack."

**Strict mode:** Spawn a separate fresh subagent. Provide:
- Full content of `${CLAUDE_PLUGIN_ROOT}/agents/validator.md`
- Spec text
- Builder artifact FILE PATHS (Validator reads them itself)
- Cognitive rules
- Domain patterns (if selected)
- Output directory: `.spec-review/rounds/round-{N}/`
- Detected language

Wait for completion. Verify Validator wrote: `validator-findings.md`, `validator-questions.md`, `validator-deadends.md`.

#### 6d: Round Summary

Generate `.spec-review/rounds/round-{N}/round-summary.md`:
```
## Round {N} Summary

Builder: {X} scenarios ({Y} new, {Z} updated)
Validator: {A} findings ({B} blockers, {C} high, {D} medium, {E} low)
New dead-ends: {F}
New open questions: {G}
Resolved from previous round: {H}

Delta from round {N-1}: [improving / stable / degrading]
```

Show summary to user.

**Early stop conditions:**
- If Validator found zero new issues AND this is round 2+: ask user "Validator found no new issues. Continue remaining rounds or stop here?"
- If Validator found zero issues on round 1: warn "Finding zero issues on round 1 is suspicious (Pipeline v3 cognitive bug #1). Consider re-running with stricter parameters or switching to --mode strict."

**Token budget check:** After round 1, estimate total token cost for remaining rounds. If estimated cost > 2x round 1 cost, inform user before continuing.

### Step 7: Report Generation

After all rounds complete, read `${CLAUDE_SKILL_DIR}/references/report-format.md` for the template.

Generate `.spec-review/output/report.md` **in the detected language** with these sections:

1. **Executive Summary** — 2-3 sentences. What was reviewed, key risk, verdict. A judgment, not a feature list.
2. **Verdict** — READY / NEEDS_WORK / NOT_READY (always English keyword).
   - READY: 0 blockers, 0 dead-ends, few high findings
   - NEEDS_WORK: 0 blockers, but dead-ends or >3 high findings
   - NOT_READY: any blockers present
   - If Validator found 0 issues: verdict includes warning about suspicious result
3. **Findings by Severity** — BLOCKER, HIGH, MEDIUM, LOW. Each with ID, title, description, impacted scenarios, proposed options.
4. **Dead-End States** — State, why stuck, real-world consequence, who suffers.
5. **Open Questions** — Question, impacted scenarios, options A/B/C, severity.
6. **Decision Log** — For each round: what Builder proposed, what Validator attacked, which findings were accepted/rejected and why. This is the Builder-Validator debate log needed for Pipeline v3 Gate 4.
7. **State Transitions** — Text table (From / To / Trigger / Condition). No ASCII art.
8. **BDD Scenarios** — Final consolidated scenarios with confidence tags.
9. **Coverage Map** — Feature areas with scenario counts. Honestly flag zero-coverage areas.
10. **Glossary** — Domain terms with boundaries.
11. **Round Progression** — Round-by-round delta showing convergence or divergence.

Generate `.spec-review/output/summary.json`:
```json
{
  "spec_source": "filename or 'inline prompt'",
  "spec_words": 12000,
  "language": "en",
  "domain": "government",
  "mode": "strict",
  "rounds_completed": 4,
  "scenarios_total": 46,
  "scenarios_solid": 20,
  "scenarios_inferred": 15,
  "scenarios_gap": 11,
  "open_questions": 15,
  "dead_ends": 4,
  "findings_blocker": 2,
  "findings_high": 4,
  "findings_medium": 5,
  "findings_low": 3,
  "coverage_gaps": ["search", "reporting", "archiving"],
  "verdict": "NOT_READY"
}
```

Numbers in summary.json MUST match the report content.

Report to user: verdict, key stats, path to full report.

### Step 8: Cleanup and Output

If the run completes successfully:
- Report paths to user: `.spec-review/output/report.md` and `.spec-review/output/summary.json`
- Round artifacts remain in `.spec-review/rounds/` for reference

If the run fails at any step:
- Report what succeeded and what failed
- Leave partial artifacts for debugging
- Do not silently delete or hide errors

## Error Handling

- **Builder produces empty output:** Report error, suggest user check spec readability. Do not proceed to Validator.
- **Validator subagent fails to spawn (Strict mode):** Retry once. If fails again, fall back to Fast mode with warning.
- **Token exhaustion mid-round:** Summarize previous round artifacts before feeding to next round. If still too large, report honestly: "Spec too large for single-pass analysis at this round count. Try fewer rounds or split the spec."
- **DOCX conversion fails:** Try all 3 fallbacks. If all fail, clean up `.spec-review/` and report clear error with conversion suggestions.
- **Stale artifacts from previous run:** At start, check if `.spec-review/` exists. If yes, ask user: "Previous review artifacts found. Overwrite or create new directory (.spec-review-2)?"

## Concurrent Run Protection

Use `${CLAUDE_SESSION_ID}` in the working directory path if multiple sessions may run simultaneously: `.spec-review-${CLAUDE_SESSION_ID}/`. Default to `.spec-review/` for single-user scenarios.
