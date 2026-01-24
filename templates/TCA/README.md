# TCA Templates

This folder contains template files for generating TCA (The Composable Architecture) code.

## Template List

| File | Purpose |
|------|---------|
| `TCA-Feature.swift.template` | Generate Feature (State, Action, Reducer) |
| `TCA-View.swift.template` | Generate SwiftUI View |
| `TCA-Dependency.swift.template` | Generate @DependencyClient based dependency |
| `TCA-FeatureTests.swift.template` | Generate Swift Testing based tests |
| `TCA-NavigationStack.swift.template` | Stack-based navigation pattern |
| `TCA-ChildFeature.swift.template` | Child feature composition with Scope |
| `TCA-AsyncEffect.swift.template` | Async operations with .run effects |
| `TCA-Binding.swift.template` | Two-way binding with BindableAction |
| `TCA-AlertSheet.swift.template` | Alert/ConfirmationDialog/Sheet patterns |

## Pattern Selection Guide

| Need | Template |
|------|----------|
| New standalone feature | `TCA-Feature.swift.template` |
| Feature with navigation stack | `TCA-NavigationStack.swift.template` |
| Compose child into parent | `TCA-ChildFeature.swift.template` |
| API calls / async operations | `TCA-AsyncEffect.swift.template` |
| Form inputs / user settings | `TCA-Binding.swift.template` |
| Alerts, dialogs, sheets | `TCA-AlertSheet.swift.template` |
| Network/data client | `TCA-Dependency.swift.template` |
| Feature tests | `TCA-FeatureTests.swift.template` |

## Placeholder Description

### Common Placeholders
- `{FeatureName}`: Feature name (e.g., `Search`, `Profile`)
- `{ClientName}`: Client struct name (e.g., `AuthClient`)
- `{clientName}`: Client property name (e.g., `authClient`)

### TCA-Feature.swift.template
- `{StateProperties}`: State properties
- `{ViewActions}`: User interaction actions
- `{InternalActions}`: Async response actions

### TCA-Dependency.swift.template
- `{ErrorName}`: Error enum name (e.g., `AuthError`)

### TCA-FeatureTests.swift.template
- `{ActionGroup}`: Test group name
- `{ActionName}`: Action name to test
- `{action}`: Actual action case
- `{response}`: Async response action

### TCA-NavigationStack.swift.template
- `{ParentFeatureName}`: Parent feature name

### TCA-ChildFeature.swift.template
- `{ParentFeatureName}`: Parent feature name
- `{ChildFeatureName}`: Child feature name

## File Location Rules

| Type | Path |
|------|------|
| Feature | `Feature/{Name}/{Name}Feature.swift` |
| View | `Feature/{Name}/{Name}View.swift` |
| Dependency | `Dependencies/{ClientName}.swift` |
| Tests | `Tests/Features/{Name}/{Name}FeatureTests.swift` |

## Usage

The sub-agents (`tca-feature-generator`, `tca-test-writer`) automatically reference these templates when generating TCA code.

Simply request:
```
"Create a SearchFeature"
"Add navigation stack to HomeFeature"
"Create a form feature with bindings"
```

The agent will read the templates and generate code following your project's conventions.
