# Builder-Validator

A Claude Code plugin that runs adversarial Builder-Validator cycles on requirements to find gaps, dead-end states, and missing requirements — before writing any code.

Builder creates BDD scenarios with MUST NOT sections, state machines, and coverage maps. Validator attacks them from a fresh perspective. Repeat for N rounds. Get a report with verdict: READY / NEEDS_WORK / NOT_READY.

## Real-World Results

Tested on a 46-page government EDI specification:
- 100+ BDD scenarios across 7 feature sets
- 15 open questions the spec had no answer for
- 4 dead-end states where documents get permanently stuck
- Missing areas discovered: search, reporting, user management, archiving

## Installation

```bash
git clone https://github.com/evogroup-ai/builder-validator.git
ln -sf "$(pwd)/builder-validator" ~/.claude/plugins/builder-validator
```

## Usage

```bash
# Review a specification document
/builder-validator path/to/spec.pdf

# Review with domain-specific patterns
/builder-validator spec.md --domain banking --rounds 4

# Strict mode (full Builder-Validator isolation via subagents)
/builder-validator spec.docx --mode strict --rounds 3

# Review a free-form idea (no document needed)
/builder-validator "Online store with cart, checkout, and inventory management"
```

### Input Formats

- **Markdown / Text** — read directly
- **PDF** — native support, paginated reading
- **DOCX** — converted via pandoc (primary), python-docx, or zipfile fallback
- **Inline prompt** — free-form ideas, no document required
- **Screenshots** — multimodal image analysis
- **Mixed** — any combination of the above

### Supported Domains

Optional `--domain` flag enhances findings with industry-specific attack patterns:

- `banking` — threshold splitting, KYC/AML, concurrent balance checks, sanctions timing
- `telecom` — prepaid deduction races, plan changes mid-session, number portability
- `government` — approval chain manipulation, document lifecycle dead-ends, archival policies
- `healthcare` — consent changes mid-treatment, prescription chain breaks, emergency overrides

Works on **any domain** even without the flag — generic attack patterns (dead-ends, role conflicts, timing collisions) apply universally.

### Modes

- **Fast** (default) — single-process, Builder and Validator share context. Quick, cheaper, good for most specs.
- **Strict** — Builder and Validator run as isolated subagents with zero shared context. Better adversarial quality, higher token cost.

## Output

```
.spec-review/
├── rounds/round-N/          # Per-round Builder + Validator artifacts
├── output/report.md          # Human-readable report (in spec's language)
└── output/summary.json       # Machine-readable summary
```

### Report Sections

1. Executive Summary — 2-3 sentence verdict for stakeholders
2. Verdict — READY / NEEDS_WORK / NOT_READY with justification
3. Findings by Severity — BLOCKER, HIGH, MEDIUM, LOW
4. Dead-End States — where entities get permanently stuck
5. Open Questions — with proposed options A/B/C
6. Decision Log — Builder-Validator debate for Gate 4 review
7. State Transitions — text table of all states and transitions
8. BDD Scenarios — final scenarios with confidence tags
9. Coverage Map — feature areas with zero-coverage flagged
10. Glossary — domain terms with boundaries

### Language

Report language matches input language. Russian spec = Russian report. English spec = English report. Verdict keywords (READY/NEEDS_WORK/NOT_READY) and summary.json keys always in English.

## Methodology

Based on **EvoGroup Pipeline v3** — a compilation of BDD (Dan North, 2006), Event Storming (Brandolini, 2013), DDD (Evans, 2003), and adversarial review practices, optimized for AI-assisted development.

Key principles:
- Every scenario must have a **MUST NOT** section — without it, the scenario is not ready
- **10/10 PASS is a red flag** — zero Validator findings means insufficient adversarial rigor
- Builder reasoning is never shared with Validator — only artifacts cross the boundary
- False positives are expected (30-40%) — present all findings, human decides

## Author

Vadim Berkovich, CTO @ [EvoGroup](https://evogroup.ai)

## License

MIT
