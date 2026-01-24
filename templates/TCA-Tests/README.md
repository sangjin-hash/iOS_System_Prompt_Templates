# TCA Test Templates

Test templates for TCA (The Composable Architecture) features using Swift Testing framework.

## Template List

| File | Purpose |
|------|---------|
| `Test-StateChange.swift.template` | Simple synchronous state mutations |
| `Test-AsyncSuccess.swift.template` | Successful async operations |
| `Test-AsyncError.swift.template` | Error handling in async operations |
| `Test-Navigation.swift.template` | Navigation stack and path management |
| `Test-Binding.swift.template` | Two-way binding tests |
| `Test-Alert.swift.template` | Alert, dialog, and sheet presentations |
| `Test-Timer.swift.template` | Timer and long-running effects |
| `Test-ChildDelegate.swift.template` | Parent-child feature communication |
| `MockClient.swift.template` | Dependency mocking patterns |

## Test Selection Guide

| Testing | Template |
|---------|----------|
| State changes without effects | `Test-StateChange` |
| API calls / fetch success | `Test-AsyncSuccess` |
| Error handling | `Test-AsyncError` |
| Push/pop navigation | `Test-Navigation` |
| TextField, Toggle, Picker | `Test-Binding` |
| Alerts, dialogs, sheets | `Test-Alert` |
| Timers, debounce, polling | `Test-Timer` |
| Child delegate actions | `Test-ChildDelegate` |
| Mock dependencies | `MockClient` |

## Key Testing Concepts

### TestStore

```swift
let store = TestStore(initialState: Feature.State()) {
    Feature()
} withDependencies: {
    $0.client = .mock(...)
}
```

### Actions

| Method | Use Case |
|--------|----------|
| `store.send(.action)` | User-initiated actions |
| `store.receive(.action)` | Effect-generated actions |

### State Assertions

```swift
await store.send(.action) {
    // Only assert properties that CHANGE
    $0.changedProperty = expectedValue
}
```

### Exhaustivity

By default, TestStore requires exhaustive action matching. For non-exhaustive testing:

```swift
store.exhaustivity = .off
```

## TDD Workflow

1. **RED**: Write failing test → verify it fails for the right reason
2. **GREEN**: Write minimal code to pass
3. **REFACTOR**: Clean up while keeping tests green

## Test Coverage Targets

| Component | Target |
|-----------|--------|
| Reducer logic | >= 90% |
| State mutations | 100% |
| Effect handling | >= 85% |
| Error cases | >= 80% |
| Edge cases | >= 80% |

## Common Testing Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Not exhausting actions | Test fails | Use `store.receive` for all effect actions |
| Wrong mutation order | Test fails | Only mutate what actually changes |
| Using live dependencies | Flaky tests | Always use `withDependencies` |
| Testing implementation | Brittle tests | Test behavior, not implementation |

## File Location

Place test files in:
```
Tests/Features/{FeatureName}/{FeatureName}FeatureTests.swift
```

## Usage

The `tca-test-writer` agent references these templates when generating tests. Request a test type and the agent will use the appropriate template.

```
"Write tests for the async fetch in ProfileFeature"
"Add navigation tests for HomeFeature"
"Create error handling tests for the API client"
```
