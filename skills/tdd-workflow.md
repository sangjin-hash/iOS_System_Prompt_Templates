---
name: ios-tdd-workflow
description: iOS Test-Driven Development workflow guide. Provides Red-Green-Refactor cycle, iOS testing considerations, and quality gates. Use when implementing features with TDD methodology.
keywords: iOS, Swift, TDD, testing, Red-Green-Refactor, XCTest, unit testing, TCA, Swift Testing
---

# iOS TDD Workflow Skill

## Purpose
A guide for applying Test-Driven Development (TDD) workflow to iOS development.

**Use this skill when**:
- Implementing iOS features using TDD methodology
- Following the Red-Green-Refactor cycle
- Ensuring test coverage while improving code quality

## ⚠️ Prerequisites

**Required Context**:
- ✅ Feature requirements are clear
- ✅ Acceptance criteria defined
- ✅ Tech stack already decided

**If NOT ready**:
- Gather requirements first
- Define feature specifications
- Ensure team agreement on tech stack

---

## 🔄 TDD Cycle: Red-Green-Refactor

### Overview
TDD follows a 3-step cycle:

1. **🔴 RED**: Write a failing test
   - Write a test for functionality that doesn't exist yet
   - Verify the test fails (red light)
   - Confirm the failure reason is correct (feature not implemented, not syntax error)

2. **🟢 GREEN**: Make the test pass
   - Write **minimal** code to make the test pass
   - Implementation doesn't need to be perfect
   - Verify the test passes (green light)

3. **🔵 REFACTOR**: Improve the code
   - Improve code while keeping tests green
   - Remove duplication, improve readability, optimize performance
   - Re-run tests after each refactoring to ensure they still pass

### Why TDD?

**Benefits**:
- ✅ Better design: Testable code naturally leads to good design
- ✅ Higher confidence: Prevents regression during refactoring
- ✅ Documentation: Tests document how to use the code
- ✅ Fewer bugs: Consider edge cases upfront
- ✅ Faster debugging: Immediately identify where issues occur

**Trade-offs**:
- ⚠️ Initial slowdown: May be slower initially
- ⚠️ Learning curve: Time needed to get comfortable with TDD
- ⚠️ Discipline required: Requires discipline to write tests first

---

## 📱 iOS-Specific TDD Considerations

### Testing Tools
- **XCTest**: Apple's default testing framework
- **XCUITest**: UI testing (E2E)
- **Quick/Nimble**: BDD-style testing (optional)

### Test Types
1. **Unit Tests**: ViewModel, Model, Business Logic
2. **Integration Tests**: API calls, Database operations
3. **UI Tests**: User interaction flows

### iOS-Specific Test Scenarios
- **Memory management**: Retain cycles, memory leaks
- **Threading**: Main thread updates, background tasks
- **Lifecycle events**: viewDidLoad, viewWillAppear, state restoration
- **State management**: Combine publishers, async/await, RxSwift observables
- **Persistence**: CoreData, Realm, UserDefaults

---

## 🚀 TDD Workflow: Step-by-Step

### Step 1: 🔴 RED - Write Failing Test

**Goal**: Write a test for functionality that doesn't exist yet

**Process**:
1. Identify the **smallest unit** of functionality
2. Write a test describing the expected behavior
3. Run the test → **Must fail** (red)
4. Verify the failure reason is correct (feature not implemented, not syntax error)

**RED Phase Checklist**:
- [ ] Test clearly describes expected behavior
- [ ] Test is focused on ONE piece of functionality
- [ ] Test FAILS when run (⌘U in Xcode)
- [ ] Failure reason is "method not found" or "expected behavior not met" (not syntax error)
- [ ] Test uses Arrange-Act-Assert pattern

**Common Setup**:
- `setUp()`: Initialization code run before each test
- `tearDown()`: Cleanup code run after each test
- Mock/Stub objects: Remove external dependencies

---

### Step 2: 🟢 GREEN - Make Test Pass

**Goal**: Write the MINIMUM code to make the test pass

**Process**:
1. Write **only the minimal code** to make the test pass
2. Don't consider perfection or edge cases yet
3. Run the test → **Must pass** (green)
4. Resist the urge to refactor (that's Step 3)

**GREEN Phase Checklist**:
- [ ] Test now PASSES (⌘U shows green checkmark)
- [ ] Minimal code written (no over-engineering)
- [ ] No premature optimization
- [ ] Not worrying about code quality yet
- [ ] Code compiles without errors

**Common Mistake**:
- ❌ Writing perfect, optimized code in GREEN phase
- ✅ Write simplest code that passes, refactor later

**Important for iOS**:
- Use `XCTestExpectation` for async code
- Combine uses `.sink()` with `wait(for:timeout:)`
- async/await uses `async throws` test functions

---

### Step 3: 🔵 REFACTOR - Improve Code Quality

**Goal**: Improve code design while keeping tests green

**Process**:
1. Look for code smells (duplication, long methods, unclear names)
2. Refactor for clarity, maintainability, performance
3. **Run tests after EACH refactor** → Must stay green
4. Commit when satisfied with code quality

**Refactoring Checklist**:
- [ ] Remove duplication (DRY principle)
- [ ] Improve naming (variables, methods, types)
- [ ] Extract reusable components
- [ ] Add documentation where logic isn't obvious
- [ ] Check for iOS-specific issues:
  - [ ] Retain cycles (`[weak self]` or `[unowned self]` in closures)
  - [ ] Force unwraps (`!`) → Use `guard let` or `if let`
  - [ ] Main thread violations (UI updates on background thread)
  - [ ] Memory leaks (check with Instruments)
- [ ] Optimize performance (if needed and measurable)
- [ ] Tests still pass (⌘U)

**After Refactoring**:
- If new functionality emerges, write additional tests
- If edge cases are discovered, add tests
- If error handling is improved, add failure case tests

---

## 🎯 TDD Quality Gates

### RED Phase Quality Gate
Before moving to GREEN:
- [ ] Test written and executable
- [ ] Test FAILS for the right reason (feature not implemented, not syntax error)
- [ ] Test clearly documents expected behavior
- [ ] Test is focused (tests ONE thing)
- [ ] Test follows AAA pattern (Arrange-Act-Assert)

### GREEN Phase Quality Gate
Before moving to REFACTOR:
- [ ] Test now PASSES (⌘U shows green)
- [ ] Minimal implementation (no over-engineering)
- [ ] No new functionality beyond what test requires
- [ ] Code compiles without errors
- [ ] No compiler warnings introduced

### REFACTOR Phase Quality Gate
Before moving to next feature:
- [ ] Code is clean and readable
- [ ] No duplication (DRY)
- [ ] Good naming conventions
- [ ] iOS-specific checks passed:
  - [ ] No retain cycles
  - [ ] No force unwraps in production code
  - [ ] Main thread used for UI updates
  - [ ] Memory leaks checked with Debug Memory Graph
- [ ] Tests still pass after refactoring
- [ ] Test coverage ≥ 80% for this component

---

## 🧪 iOS Testing Best Practices

### Memory Management in Tests
- Always use `setUp()` and `tearDown()`
- Set all test properties to `nil` in `tearDown()`
- Use `[weak self]` in closures to prevent retain cycles
- Test with Debug Memory Graph to detect leaks

### Testing Async Code
- **Combine**: Use `XCTestExpectation` with `.sink()` and `wait(for:timeout:)`
- **async/await**: Use `async throws` in test function signature
- **Completion handlers**: Use `XCTestExpectation` and `expectation.fulfill()`
- Set appropriate timeout values (avoid too long or too short)

### Test Organization
- One test class per production class
- Use descriptive test method names (e.g., `testLoadUserWhenNetworkFailsThenReturnsError`)
- Group related tests using `// MARK: - Section Name`
- Keep tests independent (no shared state between tests)

### Mock/Stub Strategy
- Use protocols for dependencies (enables easy mocking)
- Create mock objects that implement protocols
- Keep mocks simple (only implement what's needed for tests)
- Consider using in-memory implementations for persistence layers

---

## 🚦 Xcode Testing Commands

### Run Tests
```bash
# Run all tests
xcodebuild test -scheme YourScheme -destination 'platform=iOS Simulator,name=iPhone 15'

# Run specific test class
xcodebuild test -scheme YourScheme -only-testing:YourAppTests/UserViewModelTests

# Run with code coverage
xcodebuild test -scheme YourScheme -enableCodeCoverage YES

# Extract coverage report
xcrun xccov view --report --only-targets YourApp.app DerivedData/.../Logs/Test/*.xcresult
```

### Xcode Shortcuts
- `⌘U`: Run all tests
- `⌃⌥⌘U`: Run tests again
- `⌘6`: Show Test Navigator
- Click diamond next to test: Run single test

---

## 📊 Test Coverage Guidelines

### Coverage Targets
- **Business Logic**: ≥ 90%
- **ViewModels/Presenters**: ≥ 85%
- **Models**: 100%
- **UI Layer**: Critical flows only
- **Utilities**: ≥ 80%

### What to Test
✅ **High Value**:
- Business logic and algorithms
- Data transformations
- State management
- Error handling
- Edge cases and boundary conditions

❌ **Low Value**:
- Simple getters/setters
- UI layout code
- Third-party library internals
- Auto-generated code

---

## 🧩 TCA Test Coverage Targets

When using The Composable Architecture (TCA), apply these specific coverage targets:

| Component | Target | Notes |
|-----------|--------|-------|
| Reducer logic | >= 90% | All action handlers |
| State mutations | 100% | Every state change must be tested |
| Effect handling | >= 85% | Success, failure, cancellation |
| Error cases | >= 80% | All error paths |
| Edge cases | >= 80% | Boundary conditions |
| Navigation | >= 90% | Push, pop, deep links |
| Bindings | >= 80% | Form inputs, settings |
| Delegate actions | 100% | Parent-child communication |

### TCA Test Selection Guide

| Testing | Use Template |
|---------|--------------|
| State changes without effects | `@templates/TCA-Tests/Test-StateChange` |
| API calls / fetch success | `@templates/TCA-Tests/Test-AsyncSuccess` |
| Error handling | `@templates/TCA-Tests/Test-AsyncError` |
| Push/pop navigation | `@templates/TCA-Tests/Test-Navigation` |
| TextField, Toggle, Picker | `@templates/TCA-Tests/Test-Binding` |
| Alerts, dialogs, sheets | `@templates/TCA-Tests/Test-Alert` |
| Timers, debounce, polling | `@templates/TCA-Tests/Test-Timer` |
| Child delegate actions | `@templates/TCA-Tests/Test-ChildDelegate` |

---

## ⚠️ Common TCA Testing Mistakes

### 1. Not Exhausting All Actions
```swift
// ❌ BAD: Missing receive for async action
await store.send(.fetchItems)
// Test fails because Effect emits action

// ✅ GOOD: Receive all emitted actions
await store.send(.fetchItems) { $0.isLoading = true }
await store.receive(._response(.success([]))) { $0.isLoading = false }
```

### 2. Wrong State Mutation Order
```swift
// ❌ BAD: Mutating wrong property first
await store.send(.fetchItems) {
    $0.items = []  // This might not change
    $0.isLoading = true  // This changes
}

// ✅ GOOD: Only mutate what actually changes
await store.send(.fetchItems) {
    $0.isLoading = true
}
```

### 3. Not Using withDependencies
```swift
// ❌ BAD: Using live dependencies in tests
let store = TestStore(initialState: Feature.State()) {
    Feature()  // Uses live dependencies!
}

// ✅ GOOD: Override dependencies
let store = TestStore(initialState: Feature.State()) {
    Feature()
} withDependencies: {
    $0.apiClient = .testValue
}
```

### 4. Testing Implementation Instead of Behavior
```swift
// ❌ BAD: Testing internal implementation
func test_internalMethodCalled() { }

// ✅ GOOD: Testing observable behavior
func test_fetchItems_updatesState() { }
```

### 5. Not Testing Timer/Debounce with TestClock
```swift
// ❌ BAD: Using real clock - flaky tests
let store = TestStore(initialState: Feature.State()) {
    Feature()
}

// ✅ GOOD: Use TestClock for deterministic tests
let clock = TestClock()
let store = TestStore(initialState: Feature.State()) {
    Feature()
} withDependencies: {
    $0.continuousClock = clock
}
await clock.advance(by: .seconds(1))
```

### 6. Forgetting to Test Error Paths
```swift
// ❌ BAD: Only testing success
func test_fetch_success() { }

// ✅ GOOD: Test both success and failure
func test_fetch_success() { }
func test_fetch_failure_showsError() { }
func test_fetch_failure_preservesExistingData() { }
```

---

## ⚠️ Common TDD Pitfalls (iOS)

### 1. Testing Implementation Instead of Behavior
- ❌ **Bad**: Testing internal implementation details (whether private methods are called)
- ✅ **Good**: Testing observable behavior (results of public API)
- **Why**: Tests should pass if behavior is the same, even if internal implementation changes

### 2. Not Testing Unhappy Paths
- ❌ **Bad**: Only testing success cases
- ✅ **Good**: Test success, failure, and edge cases
- **Test scenarios**: Network errors, invalid data, timeout, empty state, boundary values

### 3. Skipping Refactor Phase
- ❌ **Bad**: RED → GREEN → Next feature
- ✅ **Good**: RED → GREEN → REFACTOR → Next feature
- **Why**: Prevents technical debt accumulation, maintains code quality

### 4. Writing Tests That Depend on Each Other
- ❌ **Bad**: Test A depends on Test B
- ✅ **Good**: Each test runs independently
- **Solution**: Each test creates its own required data

### 5. Not Using [weak self] in Closures
- ❌ **Bad**: Retain cycles cause memory leaks
- ✅ **Good**: Use `[weak self]` to prevent circular references
- **Check**: Verify with Debug Memory Graph

### 6. Flaky Tests (Intermittent Failures)
- **Causes**: Race conditions, insufficient timeout, shared state
- **Solution**: Handle async code properly, independent tests, adequate timeout

### 7. Testing Too Much in One Test
- ❌ **Bad**: One test verifies multiple features
- ✅ **Good**: One test verifies one behavior
- **Benefit**: Easy to identify cause when test fails

---

## 🔄 TDD Workflow Summary

### For Each Feature:
1. **🔴 RED**: Write failing test → Verify it fails
2. **🟢 GREEN**: Write minimal code → Verify it passes
3. **🔵 REFACTOR**: Clean up code → Verify tests still pass
4. **Repeat** for next smallest piece of functionality

### Quality Checks:
- Tests pass consistently (no flaky tests)
- Code coverage meets targets
- No iOS-specific issues (memory leaks, threading violations)
- Code is clean and maintainable

### When to Commit:
- After completing a full RED-GREEN-REFACTOR cycle
- When all tests are green
- When code quality is acceptable
- With meaningful commit message describing what was added

---

## 📚 Additional Resources

### Apple Documentation
- [XCTest Framework](https://developer.apple.com/documentation/xctest)
- [Testing Your App](https://developer.apple.com/documentation/xcode/testing-your-app)
- [Writing Testable Code](https://developer.apple.com/documentation/xcode/improving-your-app-s-performance)

### Books
- "Test Driven Development: By Example" - Kent Beck
- "iOS Test-Driven Development by Tutorials" - raywenderlich.com
- "Growing Object-Oriented Software, Guided by Tests" - Steve Freeman

### Tools
- **Quick/Nimble**: BDD-style testing framework
- **OHHTTPStubs**: Network request stubbing
- **SnapshotTesting**: UI snapshot testing
- **Instruments**: Memory leaks and performance profiling

---

## ✅ Final TDD Checklist

Before considering a feature "TDD complete":
- [ ] All functionality implemented via RED-GREEN-REFACTOR cycles
- [ ] Test coverage ≥ 80% for business logic
- [ ] All tests pass consistently (run multiple times)
- [ ] No flaky tests
- [ ] Code refactored and clean (no code smells)
- [ ] iOS-specific quality checks passed (memory, threading)
- [ ] Documentation added where needed
- [ ] Ready for code review

---

**Remember**: TDD is a discipline, not a burden. It feels slow at first, but leads to:
- Higher quality code
- Fewer bugs in production
- Faster debugging when issues arise
- Confidence in refactoring
- Better software design
