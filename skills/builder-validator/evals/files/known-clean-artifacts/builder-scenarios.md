# BDD Scenarios — Todo Application

## Feature: Todo CRUD

### Scenario 1: Create a todo [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a user is on the todo list page
When the user creates a todo with title "Buy groceries" and due date "2025-04-10"
Then a new todo appears in the list with title "Buy groceries"
  And status is "Open"
  And due date is "2025-04-10"
  And created date is set to today

MUST NOT:
  - Allow creating a todo without a title (spec: "Title (required)")
  - Allow a title exceeding 200 characters (spec: "max 200 characters")
  - Set a default due date if none provided (spec: "optional")
```

### Scenario 2: Edit a todo [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with title "Buy groceries"
When the user edits the title to "Buy organic groceries"
Then the todo title is updated to "Buy organic groceries"

MUST NOT:
  - Allow editing to an empty title
  - Change the created date on edit
```

### Scenario 3: Delete a todo [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with title "Buy groceries"
When the user deletes the todo
Then the todo is removed from the list

MUST NOT:
  - Delete without confirmation [INFERRED — spec does not mention confirmation]
  - Leave orphaned data after deletion
```

### Scenario 4: Mark todo as done [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with status "Open"
When the user marks the todo as done
Then the todo status changes to "Done"

MUST NOT:
  - Allow marking an already-done todo as done again (idempotency question) [INFERRED]
```

### Scenario 5: Reopen a completed todo [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with status "Done"
When the user reopens the todo
Then the todo status changes to "Open"
```

### Scenario 6: Create todo with empty title [INFERRED]
**Spec ref: Section 2.2 — "Title (required, max 200 characters)"**
```gherkin
Given a user is on the todo list page
When the user tries to create a todo with empty title
Then the system rejects the creation with a validation message

BOUNDARY VALUES:
  - Title = "" (0 chars) — rejected
  - Title = "A" (1 char) — accepted
  - Title = 200 chars — accepted
  - Title = 201 chars — rejected

MUST NOT:
  - Accept an empty title silently
  - Truncate a long title without informing the user
```

## Feature: Todo List Display

### Scenario 7: List sorted by due date [SOLID]
**Spec ref: Section 2.3**
```gherkin
Given todos exist:
  | Title    | Due Date   | Status |
  | Task A   | 2025-04-15 | Open   |
  | Task B   | 2025-04-10 | Open   |
  | Task C   | 2025-04-20 | Done   |
When the user views the todo list
Then open todos appear before completed todos
  And within open todos, Task B (earlier due date) appears before Task A
  And Task C (done) appears at the bottom

EDGE CASES:
  - Todo with no due date — sorted after todos with due dates? Or first? [GAP]
  - Multiple todos with the same due date — sorted by created date per spec
```

### Scenario 8: Overdue todo display [GAP]
**Spec ref: none — spec does not mention overdue behavior**
```gherkin
Given a todo with due date 2025-04-01 and status "Open"
When the current date is 2025-04-05
Then the spec does not define any visual indication or notification for overdue todos

MUST NOT:
  - Auto-complete overdue todos
  - Auto-delete overdue todos
```
