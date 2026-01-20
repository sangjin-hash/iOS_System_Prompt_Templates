# TCA Templates

This folder contains template files for generating TCA (The Composable Architecture) code.

## Template List

| File | Purpose |
|------|---------|
| `TCA-Feature.swift.template` | Generate Feature (State, Action, Reducer) |
| `TCA-View.swift.template` | Generate SwiftUI View |
| `TCA-Dependency.swift.template` | Generate @DependencyClient based dependency |
| `TCA-FeatureTests.swift.template` | Generate Swift Testing based tests |

## Placeholder Description

### TCA-Feature.swift.template
- `{FeatureName}`: Feature name (e.g., `Search`, `Profile`)
- `{StateProperties}`: State properties
- `{ViewActions}`: User interaction actions
- `{InternalActions}`: Async response actions
- `{clientName}`: Dependency client name

### TCA-View.swift.template
- `{FeatureName}`: Feature name

### TCA-Dependency.swift.template
- `{ClientName}`: Client struct name (e.g., `AuthClient`)
- `{ErrorName}`: Error enum name (e.g., `AuthError`)
- `{clientPropertyName}`: Property name for DependencyValues (e.g., `authClient`)

### TCA-FeatureTests.swift.template
- `{FeatureName}`: Feature name
- `{ActionGroup}`: Test group name
- `{ActionName}`: Action name to test
- `{action}`: Actual action case
- `{response}`: Async response action

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
```

The agent will read the templates and generate code following your project's conventions.
