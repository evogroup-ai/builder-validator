# Cognitive Bug Prevention Rules

These rules prevent the 5 known cognitive bugs in AI-assisted spec review. Apply to both Builder and Validator.

## Rule 1: Never Invent Data
Never invent data that isn't in the spec or artifacts. Tag every assumption explicitly: INFERRED (implied) or GAP (spec is silent). Silent assumptions are invisible bugs.

## Rule 2: Reasoning Never Crosses the Boundary
Builder's reasoning is NEVER shared with Validator. Reasoning = bias. Only artifacts (files) cross the Builder-Validator boundary. In Strict mode this is enforced architecturally. In Fast mode, Validator must consciously ignore any Builder reasoning from the shared context.

## Rule 3: Validator Never Suggests Solutions
Validator exposes problems: "This is broken." Not "fix it by doing X." Solutions are the human mediator's job. If Validator suggests solutions, it's doing Builder's work and losing adversarial independence.

## Rule 4: 10/10 PASS Is a Red Flag
If Validator finds zero issues in round 1, something is wrong — probably Validator is being too agreeable (sycophancy). Flag this explicitly. Perfect scores validate prejudices, not reality.

## Rule 5: Every Open Question Must Have Options
"I don't know" is not an acceptable finding. Every open question must propose 2-3 realistic options (A, B, C) with trade-offs. The human mediator decides — but they need choices to decide between.

## Rule 6: Every Finding Must Have Severity
Unrated findings are invisible to decision makers. Every finding: BLOCKER (can't build) / HIGH (production bug) / MEDIUM (should fix) / LOW (nice to have). Severity drives priority.

## Rule 7: Blockers Are the Only GO/NO GO Metric
Everything else is prioritization. If blockers = 0, the spec CAN be built (maybe with known risks). If blockers > 0, the spec CANNOT be built without resolving them.

## Rule 8: Never Auto-Split Large Specs
Cross-section findings are the most valuable ones. A gap in module A that only manifests when combined with module B is invisible if you split them. If the spec doesn't fit in context, tell the user honestly. Don't silently truncate.

## Rule 9: False Positives Are Expected
30-40% of Validator findings will be false positives. Present all findings — the human mediator decides what's real. Never auto-filter. False negative (missing a real bug) is worse than false positive (flagging a non-bug).

## Rule 10: Domain Patterns Are Starting Points
The domain-specific attack patterns (banking, telecom, etc.) are starting points, not checklists. The most valuable findings come from unexpected angles — things the domain checklist didn't anticipate. Don't just run through the checklist mechanically.
