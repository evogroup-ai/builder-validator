# State Machine — EDI Document Lifecycle

## States

| State | Description | Terminal? |
|-------|------------|-----------|
| Draft | Document created, not submitted | No |
| On Registration | Awaiting number assignment | No |
| Registered | Number assigned | No |
| On Approval | In approval chain | No |
| Approved | All approvals received | **Potential dead-end** |
| On Execution | Assigned to executors | No |
| Executed | All executors done | No |
| Archived | Long-term storage | Yes |

## Transitions

| From | To | Trigger | Condition |
|------|-----|---------|-----------|
| Draft | On Registration | Author submits | — |
| On Registration | Registered | Registrar assigns number | — |
| Registered | On Approval | Author sends to chain | Approval chain defined |
| On Approval | Approved | Last approver approves | All in chain approved |
| On Approval | Draft | Any approver rejects | — |
| Approved | On Execution | Author creates resolution | Resolution with executors |
| On Execution | Executed | All executors complete | All tasks marked done |
| Executed | Archived | System auto-archive | After retention period |

## Dead-End Analysis

**DEAD-END: "Approved" state** — If the author does not create a resolution, the document remains in "Approved" indefinitely. No timeout, no reminder, no escalation is specified. The document is stuck.

**DEAD-END: "On Execution" with deactivated executor** — If an executor is deactivated (leaves company) while their task is pending, the document cannot reach "Executed" because not all executors can complete. No reassignment mechanism is specified.

**DEAD-END: "On Approval" with deactivated approver** — If an approver in the chain is deactivated and no substitute is designated, the document is stuck. The spec mentions substitution (Section 2.3) but does not require it.

**DEAD-END: "On Registration" with no registrar action** — If the Registration Department does not act, no timeout or escalation is specified.
