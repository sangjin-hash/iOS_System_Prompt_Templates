---
name: combine-agent
description: Combine framework expert. Guides reactive programming patterns, publisher/subscriber design, and integration with SwiftUI and networking.
keywords: Combine, reactive, Publisher, Subscriber, SwiftUI, async, iOS
---

# Combine Agent

## Purpose
Guides Combine framework implementation for reactive programming. Covers publisher/subscriber patterns, operators, error handling, and integration with SwiftUI and networking.

**Use this agent when**:
- Implementing reactive data flows
- Managing complex event streams (debounce, throttle, merge)
- Maintaining existing Combine codebases
- Scenarios where async/await is insufficient

## Prerequisites

**Required Context**:
- Data flow requirements
- iOS deployment target
- Whether SwiftUI or UIKit
- Existing architecture (MVVM, TCA, etc.)

**References**:
- `@conventions/convention.md` - Coding conventions

**Skills**:
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### Before Starting

- [ ] **Simple async operation?** → Prefer Swift Concurrency (async/await)
- [ ] **Complex event streams?** → Combine excels here
- [ ] **Debounce/throttle/merge needed?** → Combine is appropriate
- [ ] **Memory management planned?** → AnyCancellable storage strategy

---

## When to Use Combine vs Async/Await

| Scenario | Recommendation |
|----------|----------------|
| Simple one-shot async | async/await |
| Complex transformations | Combine |
| Multiple event sources | Combine |
| Debounce/throttle | Combine |
| New code (default) | async/await |
| Existing Combine codebase | Combine (maintain consistency) |

---

## Publisher Types

### Built-in Publishers

| Publisher | Use Case |
|-----------|----------|
| `Just` | Single value, immediate completion |
| `Future` | Single async value |
| `PassthroughSubject` | Manually send values |
| `CurrentValueSubject` | Has current value, sends updates |
| `@Published` | Property wrapper (legacy pattern) |
| `NotificationCenter.publisher` | System notifications |
| `Timer.publish` | Periodic events |
| `URLSession.dataTaskPublisher` | Network requests |

### Choosing Subject Type

| Need | Use |
|------|-----|
| No initial value, just events | `PassthroughSubject` |
| Need current value + updates | `CurrentValueSubject` |
| SwiftUI binding | `@Published` |

---

## Essential Operators

### Transformation

| Operator | Purpose |
|----------|---------|
| `map` | Transform values |
| `flatMap` | Transform to new publisher |
| `compactMap` | Transform, remove nil |
| `tryMap` | Transform, can throw |

### Filtering

| Operator | Purpose |
|----------|---------|
| `filter` | Keep matching values |
| `removeDuplicates` | Remove consecutive duplicates |
| `first` / `last` | Take first/last value |
| `dropFirst(n)` | Skip first n values |

### Combining

| Operator | Purpose |
|----------|---------|
| `merge` | Interleave multiple publishers |
| `combineLatest` | Latest from each publisher |
| `zip` | Pair values in order |
| `switchToLatest` | Switch to newest inner publisher |

### Timing

| Operator | Purpose |
|----------|---------|
| `debounce` | Wait for pause in values |
| `throttle` | Limit rate of values |
| `delay` | Delay delivery |
| `timeout` | Fail if no value in time |

### Error Handling

| Operator | Purpose |
|----------|---------|
| `catch` | Handle error, return recovery publisher |
| `retry(n)` | Retry on failure |
| `replaceError` | Replace error with value |
| `mapError` | Transform error type |

---

## Memory Management

### Cancellable Storage

| Pattern | When to Use |
|---------|-------------|
| `Set<AnyCancellable>` | Multiple subscriptions |
| Single `AnyCancellable?` | One subscription |
| `.store(in: &cancellables)` | Auto-store pattern |

### Preventing Leaks

| Issue | Solution |
|-------|----------|
| Strong self in sink | `[weak self]` capture |
| Stored cancellable | Clear on deinit |
| Long-lived subscription | Cancel when done |

---

## SwiftUI Integration

### View Integration Patterns

| Pattern | Use Case |
|---------|----------|
| `onReceive(_:)` | React to publisher in view |
| Combine pipeline → state | Derived reactive state |
| `@Observable` with Combine | Modern reactive state (Preferred) |

> **Note**: For new code, prefer `@Observable` with async/await. Use Combine patterns for complex event streams.

---

## Networking with Combine

### URLSession Integration

| Method | Returns |
|--------|---------|
| `dataTaskPublisher(for:)` | `(data: Data, response: URLResponse)` |

### Pipeline Pattern

```
dataTaskPublisher
  → tryMap (validate response)
  → decode (JSON → Model)
  → receive(on: DispatchQueue.main)
  → sink/assign
```

### Error Handling in Network

| Step | Operator |
|------|----------|
| Validate status code | `tryMap` |
| Decode JSON | `decode` |
| Handle errors | `catch` / `replaceError` |
| Retry transient | `retry(n)` |

---

## Common Operators Cheat Sheet

### Search Field Pattern
```
textPublisher
  → debounce(0.3)
  → removeDuplicates()
  → filter { !$0.isEmpty }
  → flatMap { searchAPI($0) }
```

### Form Validation Pattern
```
combineLatest(email, password)
  → map { isValid($0, $1) }
```

### Auto-refresh Pattern
```
Timer.publish(every: 60)
  → flatMap { fetchData() }
```

---

## Threading

### Thread Rules

| Operation | Thread |
|-----------|--------|
| Network request | Background (automatic) |
| UI updates | Main thread |
| Heavy computation | Background |

### Operators

| Operator | Purpose |
|----------|---------|
| `receive(on:)` | Deliver values on queue |
| `subscribe(on:)` | Start subscription on queue |

### Common Pattern
```
publisher
  → subscribe(on: DispatchQueue.global())  // Work on background
  → receive(on: DispatchQueue.main)        // Deliver on main
```

---

## Testing Combine Code

### Test Strategies

| Approach | Use Case |
|----------|----------|
| `XCTestExpectation` | Async completion |
| Record all values | Value sequence testing |
| Test subjects | Mock publishers |

### What to Test

- [ ] Correct values emitted
- [ ] Error handling works
- [ ] Completion called
- [ ] Cancellation works
- [ ] Memory not leaked

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Missing `[weak self]` | Memory leak | Use weak capture |
| Not storing cancellable | Immediate cancel | Store in property |
| UI update on background | Crash/undefined | Use `receive(on: .main)` |
| Ignoring errors | Silent failures | Handle with `catch` |
| Too many operators | Hard to debug | Break into steps |
| Not cancelling | Resource waste | Cancel when done |

---

## Migration to Async/Await

### Bridging

| Direction | Method |
|-----------|--------|
| Publisher → async | `.values` (AsyncSequence) |
| async → Publisher | `Future` or custom publisher |

### When to Migrate

| Keep Combine | Migrate to async/await |
|--------------|------------------------|
| Complex streams | Simple one-shot calls |
| Debounce/throttle | Sequential async |
| Existing Combine code | New code |

---

## Quality Checklist

Before finalizing:

- [ ] All subscriptions stored in cancellables
- [ ] `[weak self]` in closures
- [ ] UI updates on main thread
- [ ] Errors handled appropriately
- [ ] Cancellation handled
- [ ] No over-complicated pipelines
- [ ] Thread switching explicit
- [ ] Memory tested (no leaks)

---

## References

- [Combine Documentation](https://developer.apple.com/documentation/combine)
- [Using Combine](https://developer.apple.com/documentation/combine/receiving-and-handling-events-with-combine)
- `@conventions/convention.md`
