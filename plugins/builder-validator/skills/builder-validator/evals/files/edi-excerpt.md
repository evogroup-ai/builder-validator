# Electronic Document Interchange — Specification Excerpt

## 1. Document Lifecycle

### 1.1 Document States

A document in the system can be in one of the following states:

- **Draft**: Document created, not yet submitted
- **On Registration**: Submitted for registration, awaiting number assignment
- **Registered**: Registration number assigned
- **On Approval**: Sent to approval chain
- **Approved**: All required approvals received
- **On Execution**: Assigned to executor(s)
- **Executed**: Work completed, results attached
- **Archived**: Moved to long-term storage

### 1.2 State Transitions

- Draft -> On Registration: Author submits document
- On Registration -> Registered: Registrar assigns number
- Registered -> On Approval: Author sends to approval chain
- On Approval -> Approved: All approvers sign off
- On Approval -> Draft: Any approver rejects (returns to author)
- Approved -> On Execution: Author creates resolution and assigns executors
- On Execution -> Executed: All executors report completion
- Executed -> Archived: Automatic after retention period

### 1.3 Registration

Documents receive a unique registration number in format: TYPE-YYYY-NNNNN (e.g., ORD-2025-00142). Numbers are sequential within each type per year. Registration is performed by the Registration Department.

## 2. Approval Workflow

### 2.1 Approval Chain

The author defines an approval chain — an ordered list of approvers. Each approver reviews the document sequentially. An approver can:
- **Approve**: Document moves to next approver (or to Approved if last)
- **Reject**: Document returns to Draft state. Author must address comments and resubmit.
- **Approve with comments**: Document moves forward, but comments are recorded.

### 2.2 Approval Routing

The author can modify the approval chain at any point before the document reaches "Approved" state.

### 2.3 Substitution

If an approver is unavailable (vacation, sick leave), a substitute can be designated. The substitute has full approval authority for the duration of the absence.

## 3. Execution

### 3.1 Resolution

After a document is approved, the author creates a Resolution that specifies:
- Executor(s) — one or more employees
- Deadline for each executor
- Instructions for execution

### 3.2 Execution Process

Each executor independently works on their assignment. When complete, the executor marks their task as done and attaches a report. When all executors complete their tasks, the document moves to "Executed" state.

### 3.3 Deadline Management

If an executor misses the deadline, the system sends a notification to the author. No automatic escalation is defined.

## 4. User Roles

- **Author**: Creates documents, defines approval chains, creates resolutions
- **Registrar**: Assigns registration numbers
- **Approver**: Reviews and approves/rejects documents
- **Executor**: Performs assigned work
- **Administrator**: Manages users and system settings

## 5. Notifications

The system sends email notifications for:
- Document submitted for registration
- Document sent for approval
- Document approved or rejected
- Resolution created (to executors)
- Deadline approaching (3 days before)
- Deadline missed

## 6. Access Control

Users can only see documents where they are: author, approver, executor, or explicitly granted access. Administrators can see all documents.
