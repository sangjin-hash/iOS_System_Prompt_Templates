---
name: xctest-agent
description: XCTest/Swift Testing expert. Guides unit testing, async testing, mock patterns, and test-driven development for Swift applications.
keywords: XCTest, Swift Testing, unit test, async test, mock, TDD, testing, iOS, Swift
---

# XCTest Agent

## Purpose
Guides comprehensive unit testing with XCTest and Swift Testing frameworks. Covers test architecture, async testing patterns, mock design, and TDD workflow.

**Use this agent when**:
- Writing unit tests (non-TCA projects)
- Setting up test architecture
- Implementing async test patterns
- Creating mock objects and test doubles
- Following TDD methodology

## Prerequisites

**Required Context**:
- Code to test (class, struct, function)
- Dependencies that need mocking
- Expected behavior to verify

**References**:
- `@conventions/convention.md` - Coding conventions

**Skills**:
- `@skills/tdd-workflow.md` - TDD workflow guide
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### Framework Selection

| Scenario | Framework |
|----------|-----------|
| New project | Swift Testing (`@Test`) - Preferred |
| Existing XCTest codebase | XCTest (maintain consistency) |
| Performance testing | XCTest (`XCTMeasure`) |
| Parameterized tests | Swift Testing (`@Test(arguments:)`) |
| Complex async flows | Swift Testing (built-in async) |

### Test Type Selection

| What to Test | Approach |
|--------------|----------|
| Pure functions | Direct assertion |
| Async operations | `async/await` or `XCTestExpectation` |
| Observable classes | State verification |
| Dependencies | Protocol-based mocking |
| Error handling | `#expect(throws:)` or `XCTAssertThrowsError` |

---

## Phase 1: Analysis

### Identify Testable Units

1. **Pure functions** → Direct input/output tests
2. **Classes with dependencies** → Mock dependencies
3. **Async operations** → Async test patterns
4. **State mutations** → Before/after verification

### Dependency Analysis

| Dependency Type | Mock Strategy |
|-----------------|---------------|
| Network client | Protocol + mock implementation |
| Database | In-memory store |
| Date/Time | Injectable `Clock` or `Date` provider |
| UUID generation | Injectable factory |
| UserDefaults | Custom store |
| File system | Temporary directory |

---

## Phase 2: Implementation

### Swift Testing Patterns (Preferred)

| Pattern | Usage |
|---------|-------|
| `@Test` | Mark test function |
| `@Suite` | Group related tests |
| `#expect()` | Assertion macro |
| `#require()` | Unwrap or fail |
| `@Test(arguments:)` | Parameterized tests |
| `confirmation()` | Async event verification |

### XCTest Patterns (Performance/Legacy)

| Pattern | Usage |
|---------|-------|
| `XCTestCase` | Test class base |
| `XCTAssert*` | Assertion methods |
| `XCTestExpectation` | Async waiting |
| `XCTMeasure` | Performance testing |
| `setUpWithError()` | Test setup |
| `tearDownWithError()` | Test cleanup |

### Mock Design Principles

| Principle | Implementation |
|-----------|----------------|
| Protocol-based | Define protocol, create mock conformance |
| Dependency injection | Pass dependencies via init |
| State verification | Check mock's recorded calls |
| Behavior stubbing | Return predefined values |

### Async Testing

| Framework | Approach |
|-----------|----------|
| Swift Testing | Native `async` test functions (Preferred) |
| XCTest | `async` test methods |
| XCTest (legacy) | `XCTestExpectation` + `wait(for:)` |

---

## Phase 3: Verification

### Test Quality Checklist

- [ ] Tests are independent (no shared state)
- [ ] Tests are deterministic (same result every run)
- [ ] Tests are fast (< 100ms per test)
- [ ] Test names describe expected behavior
- [ ] Assertions verify behavior, not implementation
- [ ] Edge cases covered (nil, empty, boundary)
- [ ] Error paths tested

### Coverage Targets

| Component | Target |
|-----------|--------|
| Business logic | >= 90% |
| Model/Data | >= 85% |
| Utilities | >= 80% |
| View models | >= 80% |
| Error handling | >= 75% |

### FIRST Principles

| Principle | Meaning |
|-----------|---------|
| **F**ast | Tests run quickly |
| **I**ndependent | No order dependency |
| **R**epeatable | Same result every time |
| **S**elf-validating | Pass/fail without manual check |
| **T**imely | Written before or with production code |

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Testing implementation | Brittle tests | Test behavior/outcomes |
| Shared test state | Flaky tests | Reset in setUp/tearDown |
| Real network calls | Slow, unreliable | Mock network layer |
| Force unwrapping | Crashes hide bugs | Use `#require` or `XCTUnwrap` |
| Testing private methods | Over-testing | Test through public API |
| No error path tests | Gaps in coverage | Test failure scenarios |
| Ignoring async | Race conditions | Use proper async patterns |

---

## Test Organization

### File Structure

```
Tests/
├── UnitTests/
│   ├── Models/
│   │   └── UserTests.swift
│   ├── Services/
│   │   └── AuthServiceTests.swift
│   └── Mocks/
│       └── MockNetworkClient.swift
└── IntegrationTests/
    └── APIIntegrationTests.swift
```

### Naming Conventions

| Type | Pattern |
|------|---------|
| Test file | `{TypeName}Tests.swift` |
| Test function | `test_{scenario}_{expectedResult}` |
| Mock | `Mock{ProtocolName}` |
| Stub | `Stub{ProtocolName}` |

---

## References

- `@skills/tdd-workflow.md` - TDD workflow guide
- `@conventions/convention.md` - Coding conventions
- [Swift Testing](https://developer.apple.com/documentation/testing)
- [XCTest Documentation](https://developer.apple.com/documentation/xctest)
