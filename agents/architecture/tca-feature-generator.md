---
name: tca-feature-generator
description: TCA (The Composable Architecture) Feature boilerplate generator. Creates State, Action, Reducer, and View structure following project conventions.
keywords: TCA, Composable Architecture, Feature, Reducer, SwiftUI, boilerplate, generator
---

# TCA Feature Generator Agent

## Purpose
Generates TCA Feature boilerplate code following project conventions. Creates consistent State, Action, Reducer, and View structure.

**Use this agent when**:
- Creating a new TCA Feature
- Refactoring existing code to TCA pattern
- Maintaining consistent Feature structure

## Prerequisites

**Required Context**:
- Feature name
- Feature's main responsibilities
- Required State properties
- Main Actions

**References**:
- `@conventions/convention.md` - Coding conventions

**Templates**: `@templates/TCA/`

**Skills**: `@skills/tdd-workflow.md` (if writing tests)

---

## Decision Checklist

| Question | Yes → | No → |
|----------|-------|------|
| Standalone feature? | `TCA-Feature.swift.template` | - |
| Has navigation stack? | `TCA-NavigationStack.swift.template` | - |
| Has child features? | `TCA-ChildFeature.swift.template` | - |
| Makes async calls? | `TCA-AsyncEffect.swift.template` | - |
| Has form bindings? | `TCA-Binding.swift.template` | - |
| Shows alerts/sheets? | `TCA-AlertSheet.swift.template` | - |
| Needs dependency? | `TCA-Dependency.swift.template` | - |
| Needs tests? | `TCA-FeatureTests.swift.template` | - |

---

## Phase 1: Analysis

### Determine Feature Type

1. **Is this a root feature or child feature?**
   - Root → May need NavigationStack
   - Child → May need delegate actions

2. **What data does it manage?**
   - List data → Items array + loading + error states
   - Form data → Bindings + validation
   - Detail data → Single item + editing state

3. **What effects does it need?**
   - API calls → Async effects + dependency client
   - Timers → Clock dependency
   - Navigation → Path/destination state

4. **How does it communicate with parent?**
   - One-way → No delegate needed
   - Two-way → Delegate action enum

---

## Phase 2: Implementation

### Template Selection

| Need | Template |
|------|----------|
| Basic feature | `@templates/TCA/TCA-Feature.swift.template` |
| SwiftUI view | `@templates/TCA/TCA-View.swift.template` |
| Navigation stack | `@templates/TCA/TCA-NavigationStack.swift.template` |
| Child composition | `@templates/TCA/TCA-ChildFeature.swift.template` |
| Async operations | `@templates/TCA/TCA-AsyncEffect.swift.template` |
| Form bindings | `@templates/TCA/TCA-Binding.swift.template` |
| Alerts/sheets | `@templates/TCA/TCA-AlertSheet.swift.template` |
| Dependency client | `@templates/TCA/TCA-Dependency.swift.template` |
| Feature tests | `@templates/TCA/TCA-FeatureTests.swift.template` |

### File Organization

```
Feature/{FeatureName}/
├── {FeatureName}Feature.swift    # Reducer (State, Action, body)
└── {FeatureName}View.swift       # SwiftUI View
```

### State Design Guidelines

| Property Type | Pattern |
|---------------|---------|
| Loading indicator | `var isLoading = false` |
| Error message | `var errorMessage: String?` |
| Data list | `var items: [Item] = []` |
| Selected item | `var selectedItem: Item?` |
| Navigation path | `var path = StackState<Path.State>()` |
| Presented state | `@Presents var child: ChildFeature.State?` |
| Alert state | `@Presents var alert: AlertState<Action.Alert>?` |

### Action Design Guidelines

| Action Type | Naming Convention |
|-------------|-------------------|
| User action | Imperative verb: `buttonTapped`, `rowSelected` |
| Lifecycle | `onAppear`, `onDisappear` |
| Internal response | Prefix with underscore: `_itemsResponse` |
| Delegate | `delegate(Delegate)` enum |
| Binding | `binding(BindingAction<State>)` |
| Child | `child(ChildFeature.Action)` |
| Navigation | `path(StackActionOf<Path>)` |

---

## Phase 3: Verification

### Feature Structure Checklist

- [ ] `@Reducer` macro applied
- [ ] `@ObservableState` on State struct
- [ ] State conforms to `Equatable`
- [ ] Action conforms to `Equatable`
- [ ] Dependencies declared with `@Dependency`
- [ ] Reducer body uses `Reduce { }` pattern

### Navigation Checklist (if applicable)

- [ ] `@Reducer enum Path` defined
- [ ] `.forEach(\.path, action: \.path)` in body
- [ ] View uses `NavigationStack(path:destination:)`

### Child Feature Checklist (if applicable)

- [ ] `Scope(state:action:)` in body
- [ ] Delegate actions handled in parent
- [ ] Non-delegate actions ignored with `case .child: return .none`

### Async Effect Checklist (if applicable)

- [ ] Dependency client injected
- [ ] Loading state managed
- [ ] Error handling implemented
- [ ] Response actions handle success/failure

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Missing `@ObservableState` | State changes not observed | Add macro to State |
| Forgetting `.forEach` | Navigation doesn't work | Add to reducer body |
| Not handling delegate | Parent misses events | Handle `.delegate` cases |
| Live dependency in tests | Tests are slow/flaky | Use `withDependencies` |
| Missing Equatable on Action | Build error with Result | Implement `==` for Error cases |

---

## Output Format

When generating a Feature, output in this format:

```markdown
## Generated: {FeatureName}Feature

### Files Created
1. `Feature/{FeatureName}/{FeatureName}Feature.swift`
2. `Feature/{FeatureName}/{FeatureName}View.swift`

### State Properties
- `property1: Type` - Description
- `property2: Type` - Description

### Actions
- `action1` - Description
- `action2` - Description

### Dependencies
- `dependencyClient` - Purpose

### Next Steps
1. Implement Dependency Client (if needed)
2. Integrate with Parent Feature (if needed)
3. Connect Navigation (if needed)
4. Write tests using `@templates/TCA-Tests/`
```

---

## References

- `@templates/TCA/README.md` - Template overview
- `@conventions/convention.md` - Coding conventions
- [TCA Documentation](https://pointfreeco.github.io/swift-composable-architecture/main/documentation/composablearchitecture/)
