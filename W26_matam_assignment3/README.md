# Task Manager in C++ (Matam Homework 3)

A small task-management system written in C++: tasks are assigned to people, kept in priority order, and can be completed, re-prioritized and printed. It is built on a generic, hand-written sorted linked list with its own iterator.

| | |
|---|---|
| **Course** | Introduction to Systems Programming (234124), Technion |
| **Semester** | Winter 2025-2026 |
| **Dates** | January 8-12, 2026 |
| **Type** | Pair assignment |
| **Language** | C++ (templates, OOP, exceptions, STL-style iterators, lambdas) |

## Contribution

| Component | Files | Written by |
|---|---|---|
| `TaskManager`: task assignment, completion, priority bumping, printing | `TaskManager.cpp`, private fields in `TaskManager.h` | **Lina Nweisry** |
| Generic `SortedList<T>` with `ConstIterator`, copy constructor, assignment, `insert`, `remove`, `length`, `filter`, `apply` | `SortedList.h` | **Raaed Ashkar** |
| `Task`, `Person`, test harness, expected outputs | `Task.h/.cpp`, `Person.h/.cpp`, `main.cpp`, `tests/` | Supplied by the course in the initial upload (unchanged since) |

I implemented everything in `TaskManager.cpp` and added the private fields to `TaskManager.h`, and I used my partner's `SortedList` as the underlying data structure.

## Skills demonstrated

C++ class design, templates, linked-list data structures, custom iterators, exception safety, lambdas and callables, working with a provided interface, pair programming with Git.

## How it works

### `SortedList<T>` (`SortedList.h`, namespace `mtm`)
- Singly linked list that keeps elements in descending order using `operator>`. Equal elements are inserted after existing equal ones.
- `ConstIterator` supports `*`, `++` and `!=`. Dereferencing or advancing an end iterator throws `std::out_of_range`.
- `remove(iterator)` returns an iterator to the next element. Removing `end()` does nothing.
- The copy constructor cleans up and rethrows if allocation fails. Assignment copies into a temporary first, so it is exception-safe.
- `filter(condition)` and `apply(operation)` return new sorted lists and accept any callable (lambdas included).

### `Task` and `Person` (course-supplied)
- A `Task` has an id, a priority clamped to 0-100, one of 10 types (`TaskType`, for example `Meeting`, `Development`, `Testing`) and a description. Higher priority sorts first. Ties go to the lower id, which is the task assigned earlier.
- A `Person` has a name and a `SortedList<Task>`. It can assign a task, complete the highest-priority one (throws `std::runtime_error` if there is none), and print itself.

### `TaskManager` (my part)
- Holds up to 10 people in a fixed array, plus a counter for free slots and a counter for task ids. Copying is disabled.
- `assignTask(name, task)` gives the task a new unique id, then adds it to the matching person. If the person is new it adds them, or throws `std::runtime_error("no available space!")` when all 10 slots are used.
- `completeTask(name)` completes that person's highest-priority task.
- `bumpPriorityByType(type, amount)` raises the priority of every task of that type for all people. Each task is rebuilt with the same id and re-inserted so the list stays sorted. Non-positive amounts are ignored.
- `printAllEmployees()` prints every person with their tasks.
- `printAllTasks()` merges all tasks into one sorted list and prints it.
- `printTasksByType(type)` does the same, filtered by type with a lambda.

## Interface

```cpp
TaskManager manager;
manager.assignTask("Alice", Task(5, TaskType::Development, "Refactor core module"));
manager.bumpPriorityByType(TaskType::Development, 50);
manager.printTasksByType(TaskType::Development);
manager.completeTask("Alice");
```

## Files

| File | Purpose |
|---|---|
| `SortedList.h` | Generic sorted linked list and its iterator (header-only) |
| `Task.h`, `Task.cpp` | Task class and `TaskType` enum (course-supplied) |
| `Person.h`, `Person.cpp` | Person class (course-supplied) |
| `TaskManager.h`, `TaskManager.cpp` | Task manager interface (course-supplied) and implementation (mine) |
| `main.cpp` | Test runner with six tests for the list and the manager (course-supplied) |
| `tests/test1..6.expected` | Expected outputs for the tests (course-supplied) |

## Build and run

Compile all `.cpp` files together (`SortedList.h` is header-only), for example:

```
g++ -std=c++17 main.cpp Task.cpp Person.cpp TaskManager.cpp -o tests
./tests        # run all tests
./tests 3      # run test number 3
```
