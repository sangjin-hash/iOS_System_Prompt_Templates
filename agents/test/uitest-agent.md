---
name: uitest-agent
description: XCUITest UI testing expert. Guides UI automation, accessibility testing, Page Object pattern, and cross-device testing for iOS applications.
keywords: XCUITest, UI testing, automation, accessibility, Page Object, iOS, Swift
---

# UITest Agent

## Purpose
Guides UI testing with XCUITest framework. Covers test automation, Page Object pattern, accessibility testing, and reliable UI test strategies.

**Use this agent when**:
- Automating user flows
- Testing critical UI interactions
- Verifying accessibility compliance
- Setting up UI test architecture
- Creating screenshot tests

## Prerequisites

**Required Context**:
- App navigation structure
- Critical user flows to test
- Accessibility identifier strategy
- Target devices/screen sizes

**References**:
- `@conventions/convention.md` - Coding conventions
- `@HIG/APPLE-HIG.md` - Human Interface Guidelines (accessibility requirements)

**Skills**:
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### What to Test with UI Tests

| Test | Priority |
|------|----------|
| Critical user journeys | High |
| Authentication flows | High |
| Purchase/checkout flows | High |
| Navigation paths | Medium |
| Form submissions | Medium |
| Accessibility compliance | Medium |
| Visual regressions | Low |

### What NOT to Test with UI Tests

| Avoid | Reason | Alternative |
|-------|--------|-------------|
| Business logic | Slow, fragile | Unit tests |
| Edge cases | Expensive | Unit tests |
| API responses | Flaky | Mock + unit tests |
| All permutations | Time-consuming | Parameterized unit tests |

---

## Phase 1: Analysis

### User Flow Mapping

1. **Identify critical paths**
   - Onboarding → Main screen
   - Login → Dashboard
   - Browse → Detail → Action
   - Cart → Checkout → Confirmation

2. **Map element identifiers**
   - Buttons, fields, labels
   - Navigation elements
   - Dynamic content areas

3. **Define test data requirements**
   - Valid/invalid credentials
   - Sample content
   - Edge case data

### Accessibility Identifier Strategy

| Element Type | Identifier Pattern |
|--------------|-------------------|
| Buttons | `{screen}_{action}Button` |
| Text fields | `{screen}_{field}TextField` |
| Labels | `{screen}_{content}Label` |
| Cells | `{list}_{index}Cell` |
| Navigation | `{screen}NavBar` |

---

## Phase 2: Implementation

### Page Object Pattern

| Component | Responsibility |
|-----------|---------------|
| Screen object | Element queries, actions |
| Flow coordinator | Multi-screen navigation |
| Test case | Assertions, verification |
| Test data | Input values, expected results |

### Element Query Strategies

| Strategy | When to Use |
|----------|-------------|
| Accessibility identifier | Preferred (most stable) |
| Label text | Static text elements |
| Predicate | Complex matching |
| Element type + index | Last resort |

### Wait Strategies

| Strategy | Use Case |
|----------|----------|
| `waitForExistence(timeout:)` | Element appearance |
| `XCTNSPredicateExpectation` | Complex conditions |
| Explicit polling | Custom conditions |

### Test Data Management

| Approach | When |
|----------|------|
| App launch arguments | Configuration flags |
| Environment variables | API endpoints, tokens |
| UI interruption handlers | System dialogs |
| Reset app state | Clean test isolation |

---

## Phase 3: Verification

### UI Test Quality Checklist

- [ ] Uses accessibility identifiers (not coordinates)
- [ ] Implements Page Object pattern
- [ ] Has proper wait strategies
- [ ] Handles interruptions (alerts, permissions)
- [ ] Tests are independent
- [ ] Runs on multiple device sizes
- [ ] Includes accessibility verification

### Reliability Checklist

- [ ] No hardcoded delays (`sleep()`)
- [ ] Uses explicit waits
- [ ] Handles async content loading
- [ ] Recovers from flaky states
- [ ] Has retry mechanism for known issues

### Accessibility Testing Checklist

- [ ] VoiceOver navigation works
- [ ] All interactive elements have labels
- [ ] Touch targets >= 44x44 points
- [ ] Color contrast sufficient
- [ ] Dynamic Type supported

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Coordinate-based taps | Breaks on different devices | Use accessibility identifiers |
| `sleep()` calls | Slow, still flaky | Use `waitForExistence` |
| No Page Objects | Unmaintainable tests | Implement Page Object pattern |
| Testing everything | Slow CI, flaky | Focus on critical flows |
| Ignoring interruptions | Tests fail on dialogs | Add interruption handlers |
| Shared test state | Order-dependent tests | Reset state between tests |
| Missing identifiers | Can't find elements | Add accessibility IDs in app |

---

## Test Organization

### File Structure

```
UITests/
├── Screens/
│   ├── LoginScreen.swift
│   ├── HomeScreen.swift
│   └── SettingsScreen.swift
├── Flows/
│   └── OnboardingFlow.swift
├── Tests/
│   ├── LoginTests.swift
│   ├── OnboardingTests.swift
│   └── PurchaseTests.swift
└── Utilities/
    ├── XCUIApplication+Extensions.swift
    └── TestData.swift
```

### Screen Object Structure

| Component | Purpose |
|-----------|---------|
| Element properties | Lazy element queries |
| Action methods | User interactions |
| Verification methods | State assertions |
| Navigation methods | Screen transitions |

### Naming Conventions

| Type | Pattern |
|------|---------|
| Screen class | `{ScreenName}Screen` |
| Test file | `{Feature}Tests.swift` |
| Test function | `test_{userAction}_{expectedResult}` |
| Flow | `{FlowName}Flow` |

---

## Performance Considerations

| Practice | Benefit |
|----------|---------|
| Minimize UI tests | Faster CI pipeline |
| Parallel execution | Reduced total time |
| Selective test runs | PR-specific testing |
| Headless mode (macOS) | Faster execution |

---

## CI/CD Integration

| Aspect | Recommendation |
|--------|----------------|
| Device selection | Representative set (small, medium, large) |
| Parallelization | Distribute across simulators |
| Retry strategy | 1-2 retries for flaky tests |
| Screenshots | Capture on failure |
| Video recording | Optional for debugging |

---

## References

- `@conventions/convention.md` - Coding conventions
- `@HIG/APPLE-HIG.md` - Human Interface Guidelines
- [XCUITest Documentation](https://developer.apple.com/documentation/xctest/user_interface_tests)
- [Accessibility Testing](https://developer.apple.com/documentation/accessibility)
