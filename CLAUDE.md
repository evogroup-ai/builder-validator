# Builder-Validator Plugin Development Rules

## TDD Mandatory
- Write tests (eval specs + assertions) BEFORE writing plugin code
- Every feature starts with a failing test case
- Code is written to pass tests, not the other way around

## Code Quality
- SKILL.md body target: ~300 lines (soft limit 500)
- Agent definitions: focused, one role per agent
- References: loaded on demand, not inlined
- No domain-specific assumptions in core logic

## Testing
- Run evals after every change to agents or SKILL.md
- Content-based assertions, not just structural checks
- 3 runs minimum for regression stability
- Track pass_rate, time, tokens per iteration

## Commit Convention
- feat: new capability
- fix: bug fix
- test: eval changes
- docs: README, comments
- refactor: restructuring without behavior change
