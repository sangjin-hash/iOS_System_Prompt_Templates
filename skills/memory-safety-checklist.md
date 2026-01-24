---
name: memory-safety-checklist
description: Memory safety rules for Swift/iOS development. Covers retain cycles, optionals, threading, and common memory pitfalls.
keywords: memory safety, retain cycles, weak self, optionals, threading, Swift, iOS
---

# Memory Safety Checklist Skill

## Purpose
A comprehensive checklist for memory safety in Swift/iOS development.

**Use this skill when**:
- Reviewing code for memory issues
- Writing code with closures and delegates
- Handling async operations
- Managing object lifecycles

---

## 1. Retain Cycles

### Closures

**Check**: Closures capturing `self` should use `[weak self]` or `[unowned self]`.

```swift
// ❌ Strong reference cycle
networkManager.fetch { data in
    self.data = data  // Captures self strongly
}

// ✅ Weak self to prevent cycle
networkManager.fetch { [weak self] data in
    self?.data = data
}

// ✅ Unowned when guaranteed non-nil
networkManager.fetch { [unowned self] data in
    self.data = data  // Only if self outlives closure
}
```

### When to Use What

| Scenario | Use |
|----------|-----|
| Closure may outlive self | `[weak self]` |
| Closure never outlives self | `[unowned self]` |
| One-shot completion handlers | `[weak self]` (safer) |
| Long-lived subscriptions | `[weak self]` (required) |

### Delegates

**Check**: Delegate properties should be `weak`.

```swift
// ✅ Correct
weak var delegate: SomeDelegate?

// ❌ Creates retain cycle
var delegate: SomeDelegate?
```

### Combine Subscriptions

**Check**: Store subscriptions and use `[weak self]` in sink.

```swift
// ✅ Correct pattern
var cancellables = Set<AnyCancellable>()

publisher
    .sink { [weak self] value in
        self?.handleValue(value)
    }
    .store(in: &cancellables)
```

---

## 2. Optionals & Force Unwrapping

### Avoid Force Unwrapping

**Check**: No `!` in production code.

```swift
// ❌ Dangerous
let user = fetchUser()!

// ✅ Safe handling
guard let user = fetchUser() else { return }

// ✅ Optional chaining
let name = user?.name ?? "Unknown"
```

### Common Force Unwrap Violations

| Pattern | Risk | Fix |
|---------|------|-----|
| `dict["key"]!` | Crash if key missing | `dict["key"] ?? default` |
| `view as! CustomView` | Crash if wrong type | `view as? CustomView` |
| `array.first!` | Crash if empty | `array.first ?? default` |
| `URL(string: str)!` | Crash if invalid | Use `guard let` |

### Acceptable Force Unwraps

| Context | Reason |
|---------|--------|
| `@IBOutlet weak var view: UIView!` | Set by storyboard |
| Test code | Crashes show test failure |
| `fatalError()` paths | Intentional crash |

---

## 3. Threading Rules

### UI Updates on Main Thread

**Check**: All UI updates happen on main thread.

```swift
// ✅ Explicit main thread
DispatchQueue.main.async {
    self.updateUI()
}

// ✅ Using @MainActor
@MainActor
func updateUI() {
    label.text = "Updated"
}

// ✅ In async context
await MainActor.run {
    updateUI()
}
```

### @Observable and @MainActor

**Check**: All `@Observable` classes marked with `@MainActor`.

```swift
// ✅ Correct
@MainActor
@Observable
class ViewModel {
    var items: [Item] = []
}

// ❌ Missing @MainActor - concurrency violations
@Observable
class ViewModel {
    var items: [Item] = []
}
```

### Race Conditions

**Check**: Shared mutable state is synchronized.

```swift
// ✅ Use actor for state isolation
actor DataManager {
    private var cache: [String: Data] = [:]

    func getData(for key: String) -> Data? {
        cache[key]
    }

    func setData(_ data: Data, for key: String) {
        cache[key] = data
    }
}
```

---

## 4. Common Memory Issues

| Issue | Detection | Prevention |
|-------|-----------|------------|
| Closures capturing self | Code review | Always use `[weak self]` |
| Timer references | Memory graph | Invalidate in `deinit` |
| NotificationCenter observers | Memory graph | Remove in `deinit` |
| Long-lived strong references | Instruments | Use weak where appropriate |
| View controllers not deallocating | Memory graph | Check parent references |

---

## 5. SwiftUI Property Wrappers

| Wrapper | Ownership | Notes |
|---------|-----------|-------|
| `@State` | View owns | Creates and owns the value |
| `@Binding` | External | Two-way binding to parent's state |
| `@Environment` | External | References environment value or @Observable |
| `@Bindable` | External | Two-way binding to @Observable |

**Best Practice with @Observable**:
```swift
// ✅ Pass Observable directly or via Environment
@Observable @MainActor
class ViewModel { }

struct MyView: View {
    @Environment(ViewModel.self) var viewModel

    var body: some View {
        @Bindable var vm = viewModel
        TextField("Name", text: $vm.name)
    }
}
```

---

## 6. Task Cancellation

**Check**: Long-running tasks are cancelled when views disappear.

```swift
// ✅ SwiftUI automatic cancellation
.task {
    await loadData()  // Cancelled when view disappears
}

// ✅ Manual cancellation
var task: Task<Void, Never>?

func startTask() {
    task = Task {
        await loadData()
    }
}

func cancel() {
    task?.cancel()
}
```

---

## Quick Memory Safety Checklist

Before code review completion:

- [ ] No closures capturing `self` without `[weak self]`
- [ ] All delegates are `weak`
- [ ] No force unwrapping in production code
- [ ] UI updates on main thread
- [ ] `@Observable` classes have `@MainActor`
- [ ] Combine subscriptions use `[weak self]`
- [ ] Long-running tasks are cancellable
- [ ] Timer/notification observers removed in deinit

---

## References

- [Memory Safety - Swift Documentation](https://docs.swift.org/swift-book/LanguageGuide/MemorySafety.html)
- [Concurrency - Swift Documentation](https://docs.swift.org/swift-book/LanguageGuide/Concurrency.html)
