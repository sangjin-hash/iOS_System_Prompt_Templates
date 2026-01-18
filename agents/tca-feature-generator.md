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
- Feature name (e.g., Home, ChatList, ChatRoom)
- Feature's main responsibilities
- Required State properties
- Main Actions

**References**:
- `@.claude/docs/draft.md` - Project structure and folder locations
- `@conventions/convention-EN.md` - Coding conventions

---

## TCA Feature Structure

### File Organization

```
Presentation/Feature/{FeatureName}/
├── {FeatureName}Feature.swift    # Reducer (State, Action, body)
└── {FeatureName}View.swift       # SwiftUI View
```

---

## Feature Template

### 1. {FeatureName}Feature.swift

```swift
import ComposableArchitecture
import Foundation

@Reducer
struct {FeatureName}Feature {
    // MARK: - State
    @ObservableState
    struct State: Equatable {
        // TODO: Add state properties
        var isLoading = false
    }

    // MARK: - Action
    enum Action: Equatable {
        // View Actions
        case onAppear
        case onDisappear

        // Internal Actions
        case _setLoading(Bool)

        // Delegate Actions (for parent communication)
        // case delegate(Delegate)
        // enum Delegate: Equatable { }
    }

    // MARK: - Dependencies
    @Dependency(\.dismiss) var dismiss

    // MARK: - Body
    var body: some ReducerOf<Self> {
        Reduce { state, action in
            switch action {
            case .onAppear:
                return .none

            case .onDisappear:
                return .none

            case ._setLoading(let isLoading):
                state.isLoading = isLoading
                return .none
            }
        }
    }
}
```

### 2. {FeatureName}View.swift

```swift
import ComposableArchitecture
import SwiftUI

struct {FeatureName}View: View {
    @Bindable var store: StoreOf<{FeatureName}Feature>

    var body: some View {
        // TODO: Implement view
        Text("{FeatureName}")
            .onAppear { store.send(.onAppear) }
            .onDisappear { store.send(.onDisappear) }
    }
}

// MARK: - Preview
#Preview {
    {FeatureName}View(
        store: Store(initialState: {FeatureName}Feature.State()) {
            {FeatureName}Feature()
        }
    )
}
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

## Dependency Registration Pattern

### Core/Dependencies/{Client}Client.swift

```swift
import ComposableArchitecture
import Foundation

struct ItemClient {
    var fetchAll: @Sendable () async throws -> [Item]
    var fetch: @Sendable (String) async throws -> Item
    var create: @Sendable (Item) async throws -> Item
    var delete: @Sendable (String) async throws -> Void
}

extension ItemClient: DependencyKey {
    static let liveValue = ItemClient(
        fetchAll: {
            // Actual implementation
            try await FirestoreService.shared.fetchItems()
        },
        fetch: { id in
            try await FirestoreService.shared.fetchItem(id: id)
        },
        create: { item in
            try await FirestoreService.shared.createItem(item)
        },
        delete: { id in
            try await FirestoreService.shared.deleteItem(id: id)
        }
    )

    static let testValue = ItemClient(
        fetchAll: { [] },
        fetch: { _ in Item.mock },
        create: { $0 },
        delete: { _ in }
    )
}

extension DependencyValues {
    var itemClient: ItemClient {
        get { self[ItemClient.self] }
        set { self[ItemClient.self] = newValue }
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

## Project Feature List

Features to generate for this project:

| Feature | Location | Description |
|---------|----------|-------------|
| AppFeature | Presentation/Feature/App/ | Root Feature, auth state management |
| AuthFeature | Presentation/Feature/Auth/ | Login screen |
| MainTabFeature | Presentation/Feature/MainTab/ | Tab bar container |
| HomeFeature | Presentation/Feature/Home/ | Friend list screen |
| SearchFeature | Presentation/Feature/Search/ | User search screen |
| ChatListFeature | Presentation/Feature/ChatList/ | Chat room list screen |
| ChatRoomFeature | Presentation/Feature/ChatRoom/ | Chat room screen |

---

## Output Format

When generating a Feature, output in this format:

```markdown
## Generated: {FeatureName}Feature

### Files Created
1. `Presentation/Feature/{FeatureName}/{FeatureName}Feature.swift`
2. `Presentation/Feature/{FeatureName}/{FeatureName}View.swift`

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
- `@.claude/docs/draft.md` - Project structure
- `@conventions/convention-EN.md` - Coding conventions
