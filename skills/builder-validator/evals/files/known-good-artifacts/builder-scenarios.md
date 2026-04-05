# BDD Scenarios — EDI Document Lifecycle

## Feature: Document Registration

### Scenario 1: Successful registration [SOLID]
**Spec ref: Section 1.3**
```gherkin
Given an author has created a document in Draft state
When the author submits the document for registration
Then the document state changes to "On Registration"
And the Registration Department receives a notification
And a registrar assigns number in format TYPE-YYYY-NNNNN
And the document state changes to "Registered"
```

### Scenario 2: Registration number uniqueness [SOLID]
**Spec ref: Section 1.3**
```gherkin
Given two documents of type ORD are submitted in year 2025
When both are registered
Then each receives a unique sequential number (ORD-2025-00001, ORD-2025-00002)
And no duplicate numbers exist within the same type and year
```

## Feature: Approval Workflow

### Scenario 3: Sequential approval — all approve [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a registered document with approval chain [Manager, Director, Legal]
When Manager approves the document
Then the document moves to Director for approval
When Director approves the document
Then the document moves to Legal for approval
When Legal approves the document
Then the document state changes to "Approved"
```

### Scenario 4: Approval rejection returns to draft [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a document "On Approval" with approval chain [Manager, Director]
And Manager has already approved
When Director rejects the document with reason "Budget not allocated"
Then the document state changes to "Draft"
And the author receives a notification with rejection reason
And all previous approvals are invalidated
```

### Scenario 5: Author modifies approval chain mid-process [INFERRED]
**Spec ref: Section 2.2**
```gherkin
Given a document "On Approval" with chain [Manager, Director, Legal]
And Manager has already approved
When the author removes Director from the approval chain
Then the document skips Director and moves to Legal
```

### Scenario 6: Substitute approver during absence [SOLID]
**Spec ref: Section 2.3**
```gherkin
Given a document waiting for approval by Director
And Director is on vacation with Substitute designated as Deputy
When Deputy approves the document
Then the approval is recorded under Deputy's name with note "acting for Director"
And the document moves to the next approver
```

## Feature: Execution

### Scenario 7: Resolution with multiple executors [SOLID]
**Spec ref: Section 3.1, 3.2**
```gherkin
Given an approved document
When the author creates a resolution with executors [Alice deadline:2025-04-10, Bob deadline:2025-04-15]
Then the document state changes to "On Execution"
And Alice and Bob each receive a notification with their instructions
```

### Scenario 8: All executors complete — document executed [SOLID]
**Spec ref: Section 3.2**
```gherkin
Given a document "On Execution" with executors [Alice, Bob]
When Alice marks her task as done and attaches report
And Bob marks his task as done and attaches report
Then the document state changes to "Executed"
```

### Scenario 9: Executor misses deadline [SOLID]
**Spec ref: Section 3.3**
```gherkin
Given a document "On Execution" with executor Alice deadline 2025-04-10
When the current date passes 2025-04-10
Then the system sends a notification to the author
And the document remains in "On Execution" state
```

### Scenario 10: Document approved but no resolution created [GAP]
**Spec ref: Section 3.1 — gap: no timeout or reminder specified**
```gherkin
Given a document in "Approved" state
When no resolution is created within any timeframe
Then the document remains in "Approved" state indefinitely
And no notification is sent
And no escalation occurs
```

## Feature: Notifications

### Scenario 11: Deadline approaching notification [SOLID]
**Spec ref: Section 5**
```gherkin
Given a document "On Execution" with executor Alice deadline 2025-04-10
When the current date is 2025-04-07 (3 days before deadline)
Then the system sends a "deadline approaching" notification to Alice
```

### Scenario 12: Rejected document notification [SOLID]
**Spec ref: Section 5**
```gherkin
Given a document rejected by an approver
Then the author receives a rejection notification with the reason
```
