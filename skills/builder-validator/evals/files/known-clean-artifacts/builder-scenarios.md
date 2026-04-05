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
```

### Scenario 2: Edit a todo [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with title "Buy groceries"
When the user edits the title to "Buy organic groceries"
Then the todo title is updated to "Buy organic groceries"
```

### Scenario 3: Delete a todo [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with title "Buy groceries"
When the user deletes the todo
Then the todo is removed from the list
```

### Scenario 4: Mark todo as done [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with status "Open"
When the user marks the todo as done
Then the todo status changes to "Done"
```

### Scenario 5: Reopen a completed todo [SOLID]
**Spec ref: Section 2.1**
```gherkin
Given a todo exists with status "Done"
When the user reopens the todo
Then the todo status changes to "Open"
```

## Feature: Todo List Display

### Scenario 6: List sorted by due date [SOLID]
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
```
