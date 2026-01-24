---
name: swift-concurrency-agent
description: Swift Concurrency expert. Guides async/await, actors, structured concurrency, and MainActor patterns for modern iOS development.
keywords: async, await, actor, Task, MainActor, concurrency, iOS, Swift
---

# Swift Concurrency Agent

## Purpose
Guides Swift Concurrency implementation including async/await, actors, structured concurrency, and safe concurrent programming patterns.

**Use this agent when**:
- Implementing async/await patterns
- Designing actor-based isolation
- Managing concurrent tasks
- Ensuring thread safety
- Migrating from GCD/Combine

## Prerequisites

**Required Context**:
- Concurrency requirements
- Data isolation needs
- Existing architecture

**References**:
- `@conventions/convention.md` - Coding conventions

**Skills**:
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### Before Starting

- [ ] **Shared mutable state?** → Consider actors
- [ ] **UI updates?** → Use `@MainActor`
- [ ] **Cancellation needed?** → Plan task lifecycle

---

## Core Concepts

### When to Use What

| Need | Use |
|------|-----|
| Sequential async operations | `async/await` |
| Parallel independent tasks | `async let` / `TaskGroup` |
| Protect shared state | `actor` |
| UI updates | `@MainActor` |
| Fire-and-forget | `Task { }` |
| Cancellable work | `Task` with cancellation check |

---

## Async/Await Fundamentals

### Function Types

| Signature | Meaning |
|-----------|---------|
| `async` | Can suspend, must await |
| `throws` | Can throw errors |
| `async throws` | Both suspend and throw |

### Calling Patterns

| Context | How to Call |
|---------|-------------|
| From async function | `await function()` |
| From sync context | `Task { await function() }` |
| SwiftUI `.task` | Automatic async context |

### Error Handling

| Pattern | Use Case |
|---------|----------|
| `try await` | Propagate errors |
| `do-catch` | Handle locally |
| `try?` | Optional result |
| `Result` type | Explicit success/failure |

---

## Structured Concurrency

### Parallel Execution

| Pattern | When to Use |
|---------|-------------|
| `async let` | Fixed number of parallel tasks |
| `TaskGroup` | Dynamic number of tasks |
| `ThrowingTaskGroup` | Dynamic tasks that can fail |

### Task Group Patterns

| Need | Approach |
|------|----------|
| Collect all results | `for await` loop |
| First result wins | `group.next()` |
| Cancel on first error | Automatic with throwing group |

---

## Actors

### When to Use Actors

| Scenario | Use Actor |
|----------|-----------|
| Shared mutable state | Yes |
| Thread-safe cache | Yes |
| Simple data container | No (use struct) |
| Immutable data | No |

### Actor Isolation

| Access | Requirement |
|--------|-------------|
| From outside actor | `await` required |
| Within actor | Direct access |
| `nonisolated` method | No await needed |

### Global Actors

| Actor | Purpose |
|-------|---------|
| `@MainActor` | UI thread isolation |
| Custom global actor | App-specific isolation domain |

---

## @MainActor Usage

### When to Apply

| Component | @MainActor |
|-----------|------------|
| `@Observable` class | Required |
| ViewModel/Store | Recommended |
| UI-updating methods | Required |
| View body | Implicit |

### Application Patterns

| Pattern | Effect |
|---------|--------|
| `@MainActor class` | Entire class on main |
| `@MainActor func` | Single method on main |
| `MainActor.run { }` | Execute block on main |

---

## Task Management

### Task Types

| Type | Characteristics |
|------|-----------------|
| `Task { }` | Inherits context, unstructured |
| `Task.detached { }` | No inherited context |
| Structured (async let, group) | Automatic cancellation |

### Cancellation

| Check | Purpose |
|-------|---------|
| `Task.checkCancellation()` | Throw if cancelled |
| `Task.isCancelled` | Check without throwing |
| `withTaskCancellationHandler` | Custom cleanup |

### Task Lifecycle

| Event | Action |
|-------|--------|
| View appears | Start task (`.task` modifier) |
| View disappears | Automatic cancellation |
| Manual cancel | Call `task.cancel()` |

---

## Sendable

### What Must Be Sendable

| Crossing | Requirement |
|----------|-------------|
| Actor boundary | Sendable |
| Task creation | Sendable closure |
| Between isolation domains | Sendable |

### Making Types Sendable

| Type | Sendable Status |
|------|-----------------|
| Value types (immutable) | Usually Sendable |
| Classes | Need explicit conformance |
| Actors | Automatically Sendable |
| Functions | `@Sendable` closure |

---

## Common Patterns

### Network Call Pattern
```
async throws → fetch data → decode → return
```

### UI Update Pattern
```
@MainActor → async work → update state
```

### Cache Pattern
```
actor Cache → check → fetch if missing → store → return
```

### Debounce Pattern
```
Task cancel previous → delay → execute
```

---

## SwiftUI Integration

### Task Modifier

| Modifier | Behavior |
|----------|----------|
| `.task { }` | Run on appear, cancel on disappear |
| `.task(id:)` | Restart when id changes |
| `.refreshable { }` | Pull-to-refresh async |

### Observable Pattern

| Component | Role |
|-----------|------|
| `@Observable @MainActor` | Thread-safe observable |
| `async` methods | Async operations |
| State properties | UI binding |

---

## Error Handling Patterns

### Propagation Strategy

| Level | Handling |
|-------|----------|
| Network layer | Throw typed errors |
| Service layer | Transform/wrap errors |
| ViewModel | Catch, set error state |
| View | Display error UI |

### Retry Pattern

| Approach | Implementation |
|----------|----------------|
| Simple retry | Loop with count |
| Exponential backoff | Increasing delay |
| Conditional retry | Check error type |

---

## Migration from GCD

### Equivalents

| GCD | Swift Concurrency |
|-----|-------------------|
| `DispatchQueue.main.async` | `@MainActor` / `MainActor.run` |
| `DispatchQueue.global().async` | `Task { }` or `Task.detached` |
| `DispatchGroup` | `TaskGroup` |
| `DispatchSemaphore` | Actor (for state) |
| Serial queue (state) | Actor |

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Missing `@MainActor` on Observable | Concurrency warnings | Add `@MainActor` |
| Blocking in async context | Thread starvation | Use async APIs |
| Ignoring cancellation | Wasted work | Check `Task.isCancelled` |
| Data race with class | Undefined behavior | Use actor or Sendable |
| Fire-and-forget without handling | Lost errors | Handle or propagate |
| Capturing non-Sendable | Compiler warning | Make Sendable or restructure |

---

## Performance Guidelines

| Guideline | Reason |
|-----------|--------|
| Don't block in async | Cooperative threading |
| Check cancellation in loops | Responsive cancellation |
| Use appropriate parallelism | Too many tasks = overhead |
| Avoid unnecessary actor hops | Performance cost |

---

## Testing Async Code

### Test Patterns

| Pattern | Use |
|---------|-----|
| `async` test method | Direct await |
| `XCTestExpectation` | Legacy/complex scenarios |
| Mock actors | Isolated testing |

### What to Test

- [ ] Success path
- [ ] Error handling
- [ ] Cancellation behavior
- [ ] Concurrent access (actors)
- [ ] MainActor isolation

---

## Quality Checklist

Before finalizing:

- [ ] `@MainActor` on Observable classes
- [ ] Proper error handling (no silent failures)
- [ ] Cancellation support where appropriate
- [ ] Sendable conformance where needed
- [ ] No data races (use actors for shared state)
- [ ] No blocking calls in async context
- [ ] Task lifecycle managed properly
- [ ] Tests cover async behavior

---

## References

- [Swift Concurrency](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
- [Actors](https://developer.apple.com/documentation/swift/actor)
- [Updating for Swift 6](https://www.swift.org/migration/documentation/migrationguide/)
- `@conventions/convention.md`
