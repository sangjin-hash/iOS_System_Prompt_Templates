---
name: tca-test-writer
description: TCA (The Composable Architecture) test code generator following TDD workflow. Generates TestStore-based tests, mocks dependencies, and follows Red-Green-Refactor cycle.
keywords: TCA, testing, TDD, TestStore, XCTest, mock, stub, Red-Green-Refactor, Swift
---

# TCA Test Writer Agent

## Purpose
Generates test code for TCA Features following Test-Driven Development (TDD) methodology. Creates TestStore-based tests, dependency mocks, and follows the Red-Green-Refactor cycle.

**Use this agent when**:
- Writing tests for TCA Features
- Following TDD workflow (write tests first)
- Creating mock dependencies for testing
- Verifying Reducer logic and Effects

## Prerequisites

**Required Context**:
- Feature name and location
- Feature's State, Action, and Dependencies
- Expected behavior to test

**References**:
- `@.claude/skills/tdd-workflow.md` - TDD workflow guide (Red-Green-Refactor)
- `@.claude/agents/tca-feature-generator.md` - TCA Feature structure
- `@conventions/convention-EN.md` - Coding conventions

---

## TDD Cycle for TCA (from tdd-workflow.md)

### Overview
Follow the 3-step cycle for each test case:

1. **RED**: Write a failing test
   - Define expected State changes
   - Define expected Action flow
   - Run test → Must fail

2. **GREEN**: Make the test pass
   - Implement minimal Reducer logic
   - Run test → Must pass

3. **REFACTOR**: Improve code quality
   - Clean up Reducer and test code
   - Run test → Must still pass

---

## TCA Testing Fundamentals

### TestStore Basics

```swift
import ComposableArchitecture
import XCTest

@MainActor
final class FeatureTests: XCTestCase {
    func testExample() async {
        let store = TestStore(initialState: Feature.State()) {
            Feature()
        }

        await store.send(.someAction) {
            $0.someProperty = expectedValue
        }
    }
}
```

### Key Concepts

| Concept | Description |
|---------|-------------|
| `TestStore` | Test harness for TCA Reducers |
| `store.send(_:)` | Send action and verify State changes |
| `store.receive(_:)` | Verify received actions from Effects |
| `withDependencies` | Override dependencies for testing |
| `exhaustivity` | Control strictness of action verification |

---

## Test File Structure

### File Location
```
ChattingExampleTests/
├── Feature/
│   ├── HomeFeatureTests.swift
│   ├── ChatListFeatureTests.swift
│   └── ChatRoomFeatureTests.swift
├── Mocks/
│   ├── MockFirestoreClient.swift
│   ├── MockKeychainClient.swift
│   └── MockSwiftDataClient.swift
└── Helpers/
    └── TestHelpers.swift
```

### Test Class Template

```swift
import ComposableArchitecture
import XCTest

@testable import ChattingExample

@MainActor
final class {FeatureName}FeatureTests: XCTestCase {

    // MARK: - Properties

    // MARK: - Setup & Teardown

    override func setUp() async throws {
        try await super.setUp()
    }

    override func tearDown() async throws {
        try await super.tearDown()
    }

    // MARK: - State Tests

    func test_initialState_isCorrect() async {
        let state = {FeatureName}Feature.State()

        XCTAssertFalse(state.isLoading)
        XCTAssertTrue(state.items.isEmpty)
        XCTAssertNil(state.errorMessage)
    }

    // MARK: - Action Tests

    // MARK: - Effect Tests

    // MARK: - Integration Tests
}
```

---

## Test Patterns

### 1. Simple State Change Test

```swift
func test_setLoading_updatesState() async {
    let store = TestStore(initialState: Feature.State()) {
        Feature()
    }

    await store.send(._setLoading(true)) {
        $0.isLoading = true
    }

    await store.send(._setLoading(false)) {
        $0.isLoading = false
    }
}
```

### 2. Async Effect Test

```swift
func test_fetchItems_success() async {
    let mockItems = [Item(id: "1", name: "Test")]

    let store = TestStore(initialState: Feature.State()) {
        Feature()
    } withDependencies: {
        $0.itemClient.fetchAll = { mockItems }
    }

    await store.send(.fetchItems) {
        $0.isLoading = true
    }

    await store.receive(._itemsResponse(.success(mockItems))) {
        $0.isLoading = false
        $0.items = mockItems
    }
}
```

### 3. Async Effect Error Test

```swift
func test_fetchItems_failure() async {
    struct TestError: Error, Equatable {}

    let store = TestStore(initialState: Feature.State()) {
        Feature()
    } withDependencies: {
        $0.itemClient.fetchAll = { throw TestError() }
    }

    await store.send(.fetchItems) {
        $0.isLoading = true
    }

    await store.receive(._itemsResponse(.failure(TestError()))) {
        $0.isLoading = false
        $0.errorMessage = "The operation couldn't be completed. (TestError error 1.)"
    }
}
```

### 4. Navigation Test

```swift
func test_itemTapped_pushesDetail() async {
    let item = Item(id: "1", name: "Test")

    let store = TestStore(
        initialState: Feature.State(items: [item])
    ) {
        Feature()
    }

    await store.send(.itemTapped(item)) {
        $0.path.append(.detail(DetailFeature.State(item: item)))
    }
}
```

### 5. Binding Test

```swift
func test_binding_textChanged() async {
    let store = TestStore(initialState: FormFeature.State()) {
        FormFeature()
    }

    await store.send(.binding(.set(\.text, "Hello"))) {
        $0.text = "Hello"
    }
}
```

### 6. Alert/Sheet Presentation Test

```swift
func test_showAlert_presentsAlert() async {
    let store = TestStore(initialState: Feature.State()) {
        Feature()
    }

    await store.send(.showDeleteAlert) {
        $0.alert = AlertState {
            TextState("Delete Item?")
        } actions: {
            ButtonState(role: .destructive, action: .confirmDelete) {
                TextState("Delete")
            }
            ButtonState(role: .cancel) {
                TextState("Cancel")
            }
        }
    }
}

func test_alertConfirmed_deletesItem() async {
    let store = TestStore(
        initialState: Feature.State(
            alert: AlertState { TextState("Delete?") }
        )
    ) {
        Feature()
    } withDependencies: {
        $0.itemClient.delete = { _ in }
    }

    await store.send(.alert(.presented(.confirmDelete))) {
        $0.alert = nil
    }

    await store.receive(.itemDeleted)
}
```

### 7. Timer/Long-Running Effect Test

```swift
func test_startTimer_emitsTicksAndStops() async {
    let clock = TestClock()

    let store = TestStore(initialState: TimerFeature.State()) {
        TimerFeature()
    } withDependencies: {
        $0.continuousClock = clock
    }

    await store.send(.startTimer) {
        $0.isTimerRunning = true
    }

    await clock.advance(by: .seconds(1))
    await store.receive(.timerTicked) {
        $0.secondsElapsed = 1
    }

    await clock.advance(by: .seconds(1))
    await store.receive(.timerTicked) {
        $0.secondsElapsed = 2
    }

    await store.send(.stopTimer) {
        $0.isTimerRunning = false
    }
}
```

### 8. Child Feature Integration Test

```swift
func test_childDelegate_updatesParent() async {
    let store = TestStore(
        initialState: ParentFeature.State(
            child: ChildFeature.State()
        )
    ) {
        ParentFeature()
    }

    await store.send(.child(.delegate(.didComplete))) {
        $0.isChildComplete = true
    }
}
```

---

## Dependency Mocking

### Mock Client Template

```swift
// Mocks/MockItemClient.swift
import ComposableArchitecture
import Foundation

@testable import ChattingExample

extension ItemClient {
    static let testValue = ItemClient(
        fetchAll: { [] },
        fetch: { _ in Item.mock },
        create: { $0 },
        delete: { _ in }
    )

    static func mock(
        fetchAll: @escaping @Sendable () async throws -> [Item] = { [] },
        fetch: @escaping @Sendable (String) async throws -> Item = { _ in Item.mock },
        create: @escaping @Sendable (Item) async throws -> Item = { $0 },
        delete: @escaping @Sendable (String) async throws -> Void = { _ in }
    ) -> Self {
        Self(
            fetchAll: fetchAll,
            fetch: fetch,
            create: create,
            delete: delete
        )
    }
}
```

### Using Mock in Tests

```swift
func test_withCustomMock() async {
    let store = TestStore(initialState: Feature.State()) {
        Feature()
    } withDependencies: {
        $0.itemClient = .mock(
            fetchAll: { [Item(id: "1", name: "Custom")] }
        )
    }

    // ... test assertions
}
```

### Project-Specific Mock Clients

| Client | Mock Behavior |
|--------|---------------|
| `FirestoreClient` | Return mock data, simulate errors |
| `KeychainClient` | In-memory storage |
| `SwiftDataClient` | In-memory database |
| `StorageClient` | Return mock URLs |

---

## TDD Quality Gates (from tdd-workflow.md)

### RED Phase Checklist
- [ ] Test clearly describes expected behavior
- [ ] Test is focused on ONE piece of functionality
- [ ] Test FAILS when run
- [ ] Failure reason is correct (feature not implemented)
- [ ] Test uses Arrange-Act-Assert pattern

### GREEN Phase Checklist
- [ ] Test now PASSES
- [ ] Minimal code written (no over-engineering)
- [ ] No new functionality beyond what test requires
- [ ] Code compiles without errors

### REFACTOR Phase Checklist
- [ ] Code is clean and readable
- [ ] No duplication (DRY)
- [ ] Good naming conventions
- [ ] iOS-specific checks:
  - [ ] No retain cycles
  - [ ] No force unwraps
  - [ ] Main thread for UI updates
- [ ] Tests still pass after refactoring

---

## Test Coverage Targets

| Component | Target |
|-----------|--------|
| Reducer logic | >= 90% |
| State mutations | 100% |
| Effect handling | >= 85% |
| Error cases | >= 80% |
| Edge cases | >= 80% |

---

## Common Testing Mistakes

### 1. Not Exhausting All Actions
```swift
// BAD: Missing receive for async action
await store.send(.fetchItems)
// Test fails because Effect emits action

// GOOD: Receive all emitted actions
await store.send(.fetchItems) { $0.isLoading = true }
await store.receive(._response(.success([]))) { $0.isLoading = false }
```

### 2. Wrong State Mutation Order
```swift
// BAD: Mutating wrong property first
await store.send(.fetchItems) {
    $0.items = []  // This might not change
    $0.isLoading = true  // This changes
}

// GOOD: Only mutate what actually changes
await store.send(.fetchItems) {
    $0.isLoading = true
}
```

### 3. Not Using withDependencies
```swift
// BAD: Using live dependencies in tests
let store = TestStore(initialState: Feature.State()) {
    Feature()  // Uses live dependencies!
}

// GOOD: Override dependencies
let store = TestStore(initialState: Feature.State()) {
    Feature()
} withDependencies: {
    $0.apiClient = .testValue
}
```

### 4. Testing Implementation Instead of Behavior
```swift
// BAD: Testing internal implementation
func test_internalMethodCalled() { }

// GOOD: Testing observable behavior
func test_fetchItems_updatesState() { }
```

---

## Output Format

When generating tests, output in this format:

```markdown
## Generated Tests: {FeatureName}Feature

### Test File
`ChattingExampleTests/Feature/{FeatureName}FeatureTests.swift`

### Test Cases
1. `test_initialState_isCorrect` - Verifies initial state
2. `test_onAppear_fetchesData` - Verifies data loading on appear
3. `test_fetchData_success` - Verifies successful fetch
4. `test_fetchData_failure` - Verifies error handling

### Mocks Created
- `MockItemClient` in `Mocks/MockItemClient.swift`

### TDD Phase
- [ ] RED: Tests written and failing
- [ ] GREEN: Implementation makes tests pass
- [ ] REFACTOR: Code cleaned up, tests still pass

### Coverage
- State mutations: X/Y covered
- Actions: X/Y covered
- Effects: X/Y covered
```

---

## References

- `@.claude/skills/tdd-workflow.md` - TDD workflow guide
- `@.claude/agents/tca-feature-generator.md` - TCA Feature structure
- [TCA Testing Documentation](https://pointfreeco.github.io/swift-composable-architecture/main/documentation/composablearchitecture/testing)
- [TestStore API](https://pointfreeco.github.io/swift-composable-architecture/main/documentation/composablearchitecture/teststore)
