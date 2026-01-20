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

**References** (MUST read before generating code):
`@conventions/convention-EN.md` - Coding conventions

**Templates** (MUST read before generating code):
- `@.claude/templates/TCA-Feature.swift.template` - Feature Reducer template
- `@.claude/templates/TCA-View.swift.template` - SwiftUI View template
- `@.claude/templates/TCA-Dependency.swift.template` - Dependency Client template

> **IMPORTANT**: Always read and follow the templates in `.claude/templates/` directory when generating TCA code. These templates reflect the project's actual conventions and patterns.

---

## TCA Feature Structure

### File Organization

```
Feature/{FeatureName}/
├── {FeatureName}Feature.swift    # Reducer (State, Action, body)
└── {FeatureName}View.swift       # SwiftUI View
```

---

## Navigation Patterns (Stack-based)

### Parent Feature with Navigation Stack

```swift
import ComposableArchitecture

@Reducer
struct ParentFeature {
    @ObservableState
    struct State: Equatable {
        var path = StackState<Path.State>()
        // other state...
    }

    enum Action: Equatable {
        case path(StackActionOf<Path>)
        // other actions...
    }

    var body: some ReducerOf<Self> {
        Reduce { state, action in
            switch action {
            case .path:
                return .none
            // handle other actions...
            }
        }
        .forEach(\.path, action: \.path)
    }

    @Reducer
    enum Path {
        case detail(DetailFeature)
        case settings(SettingsFeature)
    }
}
```

### Parent View with NavigationStack

```swift
struct ParentView: View {
    @Bindable var store: StoreOf<ParentFeature>

    var body: some View {
        NavigationStack(path: $store.scope(state: \.path, action: \.path)) {
            // Root content
            ContentView()
        } destination: { store in
            switch store.case {
            case .detail(let store):
                DetailView(store: store)
            case .settings(let store):
                SettingsView(store: store)
            }
        }
    }
}
```

---

## Child Feature Pattern

### Integrating Child Feature with Scope

```swift
@Reducer
struct ParentFeature {
    @ObservableState
    struct State: Equatable {
        var child = ChildFeature.State()
    }

    enum Action: Equatable {
        case child(ChildFeature.Action)
    }

    var body: some ReducerOf<Self> {
        Scope(state: \.child, action: \.child) {
            ChildFeature()
        }

        Reduce { state, action in
            switch action {
            case .child(.delegate(.didComplete)):
                // Handle delegate action from child
                return .none
            case .child:
                return .none
            }
        }
    }
}
```

---

## Async Effect Pattern

### API Call Example

```swift
@Reducer
struct DataFeature {
    @ObservableState
    struct State: Equatable {
        var items: [Item] = []
        var isLoading = false
        var errorMessage: String?
    }

    enum Action: Equatable {
        case fetchItems
        case _itemsResponse(Result<[Item], Error>)

        // Equatable for Error
        static func == (lhs: Action, rhs: Action) -> Bool {
            switch (lhs, rhs) {
            case (.fetchItems, .fetchItems):
                return true
            case (._itemsResponse(.success(let lhsItems)), ._itemsResponse(.success(let rhsItems))):
                return lhsItems == rhsItems
            case (._itemsResponse(.failure), ._itemsResponse(.failure)):
                return true
            default:
                return false
            }
        }
    }

    @Dependency(\.itemClient) var itemClient

    var body: some ReducerOf<Self> {
        Reduce { state, action in
            switch action {
            case .fetchItems:
                state.isLoading = true
                state.errorMessage = nil
                return .run { send in
                    do {
                        let items = try await itemClient.fetchAll()
                        await send(._itemsResponse(.success(items)))
                    } catch {
                        await send(._itemsResponse(.failure(error)))
                    }
                }

            case ._itemsResponse(.success(let items)):
                state.isLoading = false
                state.items = items
                return .none

            case ._itemsResponse(.failure(let error)):
                state.isLoading = false
                state.errorMessage = error.localizedDescription
                return .none
            }
        }
    }
}
```

---

## Binding Pattern

### Two-way Binding with TCA

```swift
@Reducer
struct FormFeature {
    @ObservableState
    struct State: Equatable {
        var text = ""
        var isToggled = false
    }

    enum Action: BindableAction, Equatable {
        case binding(BindingAction<State>)
    }

    var body: some ReducerOf<Self> {
        BindingReducer()

        Reduce { state, action in
            switch action {
            case .binding(\.text):
                // React to text changes
                return .none
            case .binding:
                return .none
            }
        }
    }
}

struct FormView: View {
    @Bindable var store: StoreOf<FormFeature>

    var body: some View {
        Form {
            TextField("Name", text: $store.text)
            Toggle("Enable", isOn: $store.isToggled)
        }
    }
}
```

---

## Alert/Sheet Pattern

### Presentation State

```swift
@Reducer
struct AlertFeature {
    @ObservableState
    struct State: Equatable {
        @Presents var alert: AlertState<Action.Alert>?
        @Presents var confirmationDialog: ConfirmationDialogState<Action.ConfirmationDialog>?
    }

    enum Action: Equatable {
        case showAlert
        case alert(PresentationAction<Alert>)
        case confirmationDialog(PresentationAction<ConfirmationDialog>)

        enum Alert: Equatable {
            case confirmTapped
            case cancelTapped
        }

        enum ConfirmationDialog: Equatable {
            case deleteTapped
            case cancelTapped
        }
    }

    var body: some ReducerOf<Self> {
        Reduce { state, action in
            switch action {
            case .showAlert:
                state.alert = AlertState {
                    TextState("Confirm Action")
                } actions: {
                    ButtonState(role: .destructive, action: .confirmTapped) {
                        TextState("Confirm")
                    }
                    ButtonState(role: .cancel, action: .cancelTapped) {
                        TextState("Cancel")
                    }
                } message: {
                    TextState("Are you sure?")
                }
                return .none

            case .alert(.presented(.confirmTapped)):
                // Handle confirm
                return .none

            case .alert:
                return .none

            case .confirmationDialog:
                return .none
            }
        }
        .ifLet(\.$alert, action: \.alert)
        .ifLet(\.$confirmationDialog, action: \.confirmationDialog)
    }
}

struct AlertView: View {
    @Bindable var store: StoreOf<AlertFeature>

    var body: some View {
        Button("Show Alert") {
            store.send(.showAlert)
        }
        .alert($store.scope(state: \.alert, action: \.alert))
        .confirmationDialog($store.scope(state: \.confirmationDialog, action: \.confirmationDialog))
    }
}
```

---

## Output Format

When generating a Feature, output in this format:

```markdown
## Generated: {FeatureName}Feature

### Files Created
1. `{FeatureName}Feature.swift`
2. `{FeatureName}View.swift`

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
```

---

## References

- [TCA Documentation](https://pointfreeco.github.io/swift-composable-architecture/main/documentation/composablearchitecture/)
