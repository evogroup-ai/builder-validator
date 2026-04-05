# Todo Application — Requirements Specification

## 1. Overview

A simple task management application for individual users. Users manage personal todos through a web interface.

## 2. Features

### 2.1 Todo Management

- Users can create a new todo with a title and optional due date
- Users can edit the title and due date of an existing todo
- Users can delete a todo
- Users can mark a todo as done
- Users can reopen a completed todo

### 2.2 Todo Properties

Each todo has:
- Title (required, max 200 characters)
- Status: Open or Done
- Due date (optional)
- Created date (auto-set)

### 2.3 Todo List

- Users see all their todos in a single list
- List is sorted by due date (earliest first), then by created date
- Completed todos appear at the bottom of the list
