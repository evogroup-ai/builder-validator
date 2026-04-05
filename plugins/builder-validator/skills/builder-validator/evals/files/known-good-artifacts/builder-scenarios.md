# BDD Scenarios — EDI Document Lifecycle

## Feature: Document Registration

### Scenario 1: Successful registration [SOLID]
**Spec ref: Section 1.3**
```gherkin
Given an author has created a document in Draft state
When the author submits the document for registration
Then the state changes to "On Registration"
  And the Registration Department receives a notification
  And a registrar assigns a number in format TYPE-YYYY-NNNNN (e.g., ORD-2025-00142)
  And the state changes to "Registered"

MUST NOT:
  - Assign a duplicate number within the same type and year
  - Lose a document between "Draft" and "On Registration" states
  - Allow registration without mandatory fields filled

EDGE CASES:
  - Two documents of the same type submitted simultaneously — numbers must be unique
  - Registrar does not act within 24 hours — no escalation defined (GAP in spec)
```

### Scenario 2: Registration number boundary [SOLID]
**Spec ref: Section 1.3**
```gherkin
Given 99,999 documents of type ORD have been registered in the current year
When the 100,000th document is registered
Then number ORD-2025-100000 is correctly assigned

BOUNDARY VALUES:
  - Number 00001 (first in year) — valid
  - Number 99999 — valid
  - Number 100000 — format NNNNN cannot fit, behavior undefined [GAP]

MUST NOT:
  - Silently truncate the number to 5 digits
  - Reset numbering without a new year starting
```

## Feature: Approval Workflow

### Scenario 3: Sequential approval — all approve [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a registered document with approval chain [Manager, Director, Legal]
When Manager approves the document
Then the document moves to Director
When Director approves the document
Then the document moves to Legal
When Legal approves the document
Then the state changes to "Approved"

MUST NOT:
  - Skip an approver in the chain
  - Reorder the approval chain without author action
  - Show previous approver's comments to the next approver (independence of evaluation) [INFERRED]
```

### Scenario 4: Approval rejection returns to draft [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a document "On Approval" with chain [Manager, Director]
  And Manager has already approved
When Director rejects with reason "Budget not allocated"
Then the state changes to "Draft"
  And the author receives a notification with the rejection reason
  And all previous approvals are invalidated

MUST NOT:
  - Preserve previous approvals after rejection
  - Allow rejection without stating a reason
  - Lose approval history (must remain available for audit)
```

### Scenario 5: Author modifies approval chain mid-process [INFERRED]
**Spec ref: Section 2.2 — "author can modify chain at any point before Approved state"**
```gherkin
Given a document "On Approval" with chain [Manager, Director, Legal]
  And Manager has already approved
When the author removes Director from the approval chain
Then the document skips Director and moves to Legal

MUST NOT:
  - Allow removing an approver who has already approved (what happens to their approval?) [GAP]
  - Allow removing ALL approvers (document reaches "Approved" without any review) [GAP]
  - Allow malicious removal of the person who would reject without an audit trail

EDGE CASES:
  - Author removes the last approver — document auto-approved? [GAP]
  - Author adds a new approver after previous ones have already approved
  - Author is also in the approval chain — self-approval (segregation of duties violation) [GAP]
```

### Scenario 6: Substitute approver during absence [SOLID]
**Spec ref: Section 2.3**
```gherkin
Given a document waiting for approval by Director
  And Director is on vacation with Deputy designated as substitute
When Deputy approves the document
Then the approval is recorded under Deputy's name with note "acting for Director"
  And the document moves to the next approver

MUST NOT:
  - Hide the substitution fact in approval history
  - Allow the substitute to act after the principal returns
  - Allow designating a substitute who is already in the chain (role conflict) [INFERRED]

EDGE CASES:
  - Director returns but Deputy has not yet approved — who has authority?
  - Deputy also goes on leave — second-level substitution? [GAP]
  - Circular substitution: A substitutes B, B substitutes A
```

### Scenario 7: Approve with comments [SOLID]
**Spec ref: Section 2.1 — "Approve with comments: document moves forward, but comments are recorded"**
```gherkin
Given a document "On Approval" with chain [Manager, Director]
When Manager approves with comments "Check budget line 42"
Then the document moves to Director
  And comments are recorded in approval history

MUST NOT:
  - Treat "approve with comments" as rejection
  - Lose the comments after the document moves forward
  - Block the approval flow because of comments

EDGE CASES:
  - Comments contradict the approval ("I approve but this is wrong") — system treats as approval per spec
  - Who sees the comments — next approver, author, both? Spec is silent [GAP]
```

## Feature: Execution

### Scenario 8: Resolution with multiple executors [SOLID]
**Spec ref: Section 3.1, 3.2**
```gherkin
Given an approved document
When the author creates a resolution with executors [Alice deadline:2025-04-10, Bob deadline:2025-04-15]
Then the state changes to "On Execution"
  And Alice and Bob each receive a notification with their instructions

MUST NOT:
  - Assign an executor without a deadline
  - Assign a deactivated employee
  - Allow an empty resolution (0 executors)
  - Assign an executor who is also an approver of this document (conflict of interest) [INFERRED]

BOUNDARY VALUES:
  - 1 executor — minimum
  - Deadline = today — is this valid?
  - Deadline in the past — should be validated [INFERRED]
```

### Scenario 9: Document approved but no resolution created [GAP]
**Spec ref: Section 3.1 — gap: no timeout or reminder specified**
```gherkin
Given a document in "Approved" state
When no resolution is created within any timeframe
Then the document remains in "Approved" state indefinitely
  And no notification is sent
  And no escalation occurs

MUST NOT:
  - Auto-transition the document without human action
  - Delete or archive a "stuck" document without notifying the author

EDGE CASES:
  - Author leaves the company — who creates the resolution?
  - 100 documents "stuck" in Approved — no dashboard or report for management [GAP]
```

### Scenario 10: Executor misses deadline [SOLID]
**Spec ref: Section 3.3**
```gherkin
Given a document "On Execution" with executor Alice, deadline 2025-04-10
When the current date passes 2025-04-10
Then the system sends a notification to the author
  And the document remains in "On Execution" state

MUST NOT:
  - Auto-remove the task from the executor
  - Auto-extend the deadline without author decision
  - Block other executors' work on this document

BOUNDARY VALUES:
  - Deadline = today 23:59 — notification at 00:00 next day?
  - 3-day warning (Section 5) + overdue notification — both sent?
  - Executor completes at 23:58 on deadline day — overdue or not?
```

## Feature: Notifications

### Scenario 11: Deadline approaching notification [SOLID]
**Spec ref: Section 5**
```gherkin
Given a document "On Execution" with executor Alice, deadline 2025-04-10
When the current date is 2025-04-07 (3 days before deadline)
Then the system sends a "deadline approaching" notification to Alice

MUST NOT:
  - Send repeated notifications every day without spec defining frequency [GAP]
  - Send a notification if the task is already completed
```

### Scenario 12: Notification delivery failure [GAP]
**Spec ref: Section 5 — gap: behavior on delivery failure not described**
```gherkin
Given the system needs to send a notification to an executor
When the email server is unavailable or the address is invalid
Then behavior is undefined in the specification

MUST NOT:
  - Silently lose a notification without logging
  - Block the business process because of a notification failure
  - Mark the task as "notified" if the notification was not delivered
```

## Feature: Document Lifecycle — Uncovered

### Scenario 13: Draft abandoned indefinitely [GAP]
**Spec ref: Section 1.2 — gap: no cleanup for abandoned drafts**
```gherkin
Given an author creates a document in Draft state
When the author never submits it for registration
Then the document remains in Draft forever
  And no cleanup or archival mechanism exists

MUST NOT:
  - Auto-delete drafts without warning (data loss risk)
  - Count abandoned drafts in active document statistics

EDGE CASES:
  - 10,000 abandoned drafts over 3 years — storage and search pollution
  - Author deactivated with 50 drafts — no ownership transfer defined [GAP]
```

### Scenario 14: Reject-resubmit infinite loop [GAP]
**Spec ref: Section 2.1 — gap: no loop detection**
```gherkin
Given a document rejected by Director
When the author resubmits without changes
  And Director rejects again
  And this repeats N times
Then no mechanism detects or breaks this cycle

MUST NOT:
  - Allow silent infinite rejection loops without alerting management
  - Auto-approve after N rejections

EDGE CASES:
  - Author makes trivial changes to bypass "same document" detection
  - Rejection reason is identical each time — system should flag pattern [INFERRED]
```
