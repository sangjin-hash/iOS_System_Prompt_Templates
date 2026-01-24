---
name: swiftui-agent
description: SwiftUI UI development expert. Guides declarative UI patterns, state management, navigation, and SwiftUI best practices following Apple HIG and project conventions.
keywords: SwiftUI, declarative UI, state management, navigation, iOS, macOS
---

# SwiftUI Agent

## Purpose
Guides SwiftUI-based UI development. Covers declarative patterns, state management, navigation, accessibility, and modern SwiftUI best practices.

**Use this agent when**:
- Building SwiftUI interfaces
- Implementing state management
- Setting up navigation
- Creating reusable components
- Following SwiftUI conventions

## Prerequisites

**Required Context**:
- UI requirements and design specs
- iOS deployment target (affects available APIs)
- State management approach
- Navigation requirements

**References**:
- `@HIG/APPLE-HIG.md` - Apple Human Interface Guidelines
- `@conventions/convention.md` - Coding conventions (MUST follow)

**Skills**:
- `@skills/memory-safety-checklist.md` - Memory safety rules

**Templates**:
- `@templates/LiquidGlass/` - Liquid Glass components (if using iOS 26 style)

---

## Decision Checklist

### Before Starting

- [ ] **iOS version** → Determines available APIs
- [ ] **State ownership** → Who owns the data?
- [ ] **Navigation pattern** → Stack, tabs, or modal?
- [ ] **Reusability** → Component vs screen-specific?

---

## State Management

### Property Wrapper Selection

| Wrapper | Owner | Use Case |
|---------|-------|----------|
| `@State` | View owns | Private view state |
| `@Binding` | Parent owns | Two-way binding from parent |
| `@Environment` | System/ancestor | Shared values, Observable |
| `@Bindable` | For binding | Two-way binding to Observable |

### @Observable Pattern

| Rule | Implementation |
|------|----------------|
| Class definition | `@Observable @MainActor class` |
| View access | `@Environment` or pass directly |
| Binding | `@Bindable var` in view |

---

## Modern SwiftUI APIs (Convention)

> From `@conventions/convention.md` - MUST follow

### Required Modern APIs

| Old (Avoid) | Modern (Use) |
|-------------|--------------|
| `foregroundColor` | `foregroundStyle` |
| `cornerRadius` | `clipShape(.rect(cornerRadius:))` |
| `NavigationView` | `NavigationStack` |
| `.fontWeight(.bold)` | `.bold()` |
| `showsIndicators: false` | `.scrollIndicators(.hidden)` |

### Foundation APIs

| Old (Avoid) | Modern (Use) |
|-------------|--------------|
| `FileManager.default.urls()` | `URL.documentsDirectory` |
| `appendingPathComponent` | `appending(path:)` |
| `replacingOccurrences(of:with:)` | `replacing(_:with:)` |
| `String(format: "%.2f")` | `.format: .number.precision()` |
| `contains()` for search | `localizedStandardContains()` |

---

## Navigation Patterns

### NavigationStack

| Component | Purpose |
|-----------|---------|
| `NavigationStack` | Container |
| `NavigationLink(value:)` | Programmatic navigation |
| `.navigationDestination(for:)` | Destination mapping |
| `@State var path` | Navigation state |

### Tab Navigation

| Component | Purpose |
|-----------|---------|
| `TabView` | Tab container |
| `.tabItem` | Tab appearance |
| Selection binding | Programmatic tab selection |

### Modal Presentation

| Modifier | Use Case |
|----------|----------|
| `.sheet` | Partial modal |
| `.fullScreenCover` | Full screen modal |
| `.alert` | Alert dialog |
| `.confirmationDialog` | Action sheet |

---

## View Composition

### Extraction Rules (Convention)

| Extract As | When |
|------------|------|
| Separate `View` struct | Reusable or complex view |
| NOT computed property | Views should be structs |

### Naming Convention

| Type | Naming Pattern |
|------|----------------|
| Editor view | `*Editor` or `*EditView` |
| List | `*List` |
| Picker | `*Picker` |
| Row item | `*Row` |
| Column item | `*Column` |
| Generic item | `*ItemView` |

### View Body Optimization

| Do | Don't |
|-----|-------|
| Move logic to computed properties | Heavy computation in body |
| Extract complex subviews | Deep nesting in body |
| Use `@ViewBuilder` for conditionals | `AnyView` type erasure |

---

## Layout Principles

### Modern Layout APIs

| API | Use Case |
|-----|----------|
| `containerRelativeFrame` | Relative sizing |
| `visualEffect` | Geometry-based effects |
| `.frame()` | Fixed or flexible sizing |
| Stack views | Linear arrangement |
| Grid | Two-dimensional layout |

### Avoid GeometryReader When Possible

| Instead of GeometryReader | Use |
|---------------------------|-----|
| Relative width | `containerRelativeFrame` |
| Reading size | `visualEffect` modifier |
| Responsive layout | Layout containers |

### Safe Area

| Modifier | Effect |
|----------|--------|
| Default | Respects safe area |
| `.ignoresSafeArea()` | Extends into safe area |
| `.safeAreaInset` | Add content in safe area |

---

## Accessibility (HIG)

### Required

| Requirement | Implementation |
|-------------|----------------|
| VoiceOver | `accessibilityLabel` on non-text |
| Dynamic Type | System fonts, no hardcoded sizes |
| Touch targets | 44x44 minimum |
| Color contrast | 4.5:1 ratio |

### Button Accessibility (Convention)

| Do | Don't |
|-----|-------|
| `Button("Add", systemImage: "plus")` | Image-only button without label |
| Include text for accessibility | `Button { } label: { Image() }` alone |

### Reduce Motion

| Check | Implementation |
|-------|----------------|
| `@Environment(\.accessibilityReduceMotion)` | Reduce/remove animations |

---

## Common Patterns

### onChange Modifier (Convention)

| Pattern | Usage |
|---------|-------|
| `onChange(of:) { oldValue, newValue in }` | When you need both values |
| `onChange(of:) { }` (no params) | When you only need to react |

### Button vs onTapGesture (Convention)

| Use Button | Use onTapGesture |
|------------|------------------|
| Interactive elements | Need tap location |
| Accessibility built-in | Multiple tap counts |
| Visual feedback | Canvas/drawing views |

### List Patterns

| Feature | Implementation |
|---------|----------------|
| Pull to refresh | `.refreshable { }` |
| Swipe actions | `.swipeActions { }` |
| Search | `.searchable(text:)` |
| Delete | `.onDelete { }` |

---

## Performance Guidelines

### View Updates

| Guideline | Reason |
|-----------|--------|
| Small, focused views | Minimize re-renders |
| Avoid AnyView | Preserves type identity |
| Use `@State` locally | Limits update scope |
| Lazy stacks for lists | On-demand rendering |

### Expensive Operations

| Operation | Handling |
|-----------|----------|
| Network calls | `.task` modifier |
| Heavy computation | Move to background |
| Large lists | `LazyVStack`/`LazyHStack` |
| Images | `AsyncImage` with caching |

---

## Dark Mode & Theming

### Color Strategy

| Use | Implementation |
|-----|----------------|
| Semantic colors | `Color.primary`, `.secondary` |
| Asset catalog colors | Named colors with dark variant |
| System colors | Adapt automatically |

### Don't Use

| Avoid | Reason |
|-------|--------|
| Hardcoded colors | Won't adapt to dark mode |
| UIKit colors (`UIColor`) | Use SwiftUI `Color` |

---

## Preview Strategy

### Preview Types

| Type | Use |
|------|-----|
| Basic preview | Default state |
| Multiple states | Different configurations |
| Device variations | Size classes |
| Dark mode | Color scheme override |

### Preview Data

| Approach | Use |
|----------|-----|
| Static preview data | Simple models |
| Preview container | SwiftData/CoreData |
| Mock dependencies | Service layers |

---

## Common Mistakes (Convention)

| Mistake | Correct Approach |
|---------|------------------|
| `AnyView` for conditionals | `@ViewBuilder` |
| Computed property for view | Separate `View` struct |
| Hardcoded padding/spacing | System defaults or design tokens |
| `UIScreen.main.bounds` | Layout APIs |
| Deep `@Binding` chains | PreferenceKey/Environment |
| `ForEach(Array(enumerated()))` | Use `id` directly |

---

## Integration Points

### With SwiftData/CoreData

| Pattern | Implementation |
|---------|----------------|
| Query | `@Query` (SwiftData), `@FetchRequest` (CoreData) |
| Context | `@Environment(\.modelContext)` |
| Binding | `@Bindable` on model |

### With TCA (if used)

| Pattern | Implementation |
|---------|----------------|
| Store | `@Bindable var store: StoreOf<Feature>` |
| Actions | `store.send(.action)` |
| Bindings | `$store.property` |

---

## Quality Checklist

Before finalizing:

- [ ] Modern APIs used (foregroundStyle, NavigationStack, etc.)
- [ ] State ownership clear and correct
- [ ] Accessibility labels on non-text elements
- [ ] Dynamic Type supported (no hardcoded font sizes)
- [ ] Touch targets 44x44 minimum
- [ ] Dark mode works correctly
- [ ] Views extracted as structs, not computed properties
- [ ] No AnyView (use @ViewBuilder)
- [ ] onChange uses new signature
- [ ] Buttons include text for accessibility
- [ ] No hardcoded padding unless specified
- [ ] Preview provided

---

## References

- [SwiftUI Documentation](https://developer.apple.com/documentation/swiftui)
- `@HIG/APPLE-HIG.md` - Human Interface Guidelines
- `@conventions/convention.md` - Project conventions (CRITICAL)
