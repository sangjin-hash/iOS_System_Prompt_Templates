---
name: uikit-agent
description: UIKit UI development expert. Guides view controller lifecycle, Auto Layout, navigation patterns, and UIKit best practices following Apple HIG.
keywords: UIKit, UIViewController, Auto Layout, UITableView, UICollectionView, navigation, iOS
---

# UIKit Agent

## Purpose
Guides UIKit-based UI development. Covers view controller patterns, Auto Layout, table/collection views, navigation, and integration with modern Swift patterns.

**Use this agent when**:
- Building UIKit-based interfaces
- Implementing complex custom views
- Working with UIKit-only features
- Integrating UIKit with SwiftUI
- Supporting iOS versions before SwiftUI features

## Prerequisites

**Required Context**:
- UI requirements and wireframes
- iOS deployment target
- Navigation requirements
- Whether hybrid with SwiftUI

**References**:
- `@HIG/APPLE-HIG.md` - Apple Human Interface Guidelines
- `@conventions/convention.md` - Coding conventions

**Skills**:
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### When to Use UIKit

- [ ] Complex custom drawing/animations
- [ ] UIKit-only APIs (e.g., advanced collection view layouts)
- [ ] Existing UIKit codebase integration
- [ ] Performance-critical list views
- [ ] Features not yet in SwiftUI

### When to Prefer SwiftUI

- [ ] New project (default choice)
- [ ] Standard UI patterns
- [ ] Rapid prototyping
- [ ] Declarative UI fits requirements

---

## View Controller Lifecycle

### Lifecycle Order

| Method | Purpose | Common Tasks |
|--------|---------|--------------|
| `init` | Initialization | Setup dependencies |
| `loadView` | Create view hierarchy | Custom root view only |
| `viewDidLoad` | View loaded (once) | Initial setup, constraints |
| `viewWillAppear` | About to appear | Refresh data, start animations |
| `viewDidAppear` | Fully visible | Start timers, analytics |
| `viewWillDisappear` | About to leave | Pause animations |
| `viewDidDisappear` | No longer visible | Stop timers, save state |
| `deinit` | Deallocation | Cleanup observers |

### Lifecycle Rules

| Rule | Reason |
|------|--------|
| Heavy setup in `viewDidLoad` | Called once |
| Refresh in `viewWillAppear` | Data may have changed |
| Start animations in `viewDidAppear` | View fully visible |
| Remove observers in `deinit` | Prevent leaks |

---

## Auto Layout Principles

### Constraint Types

| Constraint | Use Case |
|------------|----------|
| NSLayoutConstraint | Programmatic constraints |
| Visual Format Language | Multiple constraints at once |
| Layout anchors | Modern, readable approach |
| Stack views | Automatic spacing/alignment |

### Best Practices

| Practice | Reason |
|----------|--------|
| `translatesAutoresizingMaskIntoConstraints = false` | Required for programmatic |
| Use anchors over NSLayoutConstraint | More readable |
| Prefer Stack Views | Less constraint management |
| Set priorities for flexibility | Avoid conflicts |

### Common Patterns

| Pattern | When to Use |
|---------|-------------|
| Pinning to safe area | Content that shouldn't overlap notch |
| Pinning to superview | Full-bleed content |
| Intrinsic content size | Labels, buttons |
| Compression/hugging priority | Control shrink/stretch |

---

## Navigation Patterns (HIG)

### Navigation Types

| Type | When to Use | UIKit Implementation |
|------|-------------|---------------------|
| Hierarchical | Drill-down content | `UINavigationController` |
| Flat | Top-level sections | `UITabBarController` |
| Modal | Focused tasks | `present(_:animated:)` |

### Navigation Controller

| Practice | Guideline |
|----------|-----------|
| Clear titles | Each screen has descriptive title |
| Back button | Shows previous screen title |
| Large titles | Top-level screens only |
| Right bar items | Primary actions |

### Modal Presentation

| Style | Use Case |
|-------|----------|
| `.automatic` | System decides |
| `.pageSheet` | Default (partial modal) |
| `.fullScreen` | Immersive content |
| `.formSheet` | iPad forms |

---

## Table View Best Practices

### Performance

| Technique | Purpose |
|-----------|---------|
| Cell reuse | Memory efficiency |
| `dequeueReusableCell` | Always use, never create new |
| Estimated row height | Smooth scrolling |
| Prefetching | Load data ahead |

### Modern Approaches

| API | Use Case |
|-----|----------|
| Diffable data source | Automatic animations |
| Compositional layout | Complex layouts |
| Cell configurations | Modern cell setup |

### Cell Configuration

| Approach | When |
|----------|------|
| `UIListContentConfiguration` | Standard cell layouts |
| Custom cell subclass | Complex custom layouts |
| `contentConfiguration` | Modern, recommended |

---

## Collection View Best Practices

### Layout Types

| Layout | Use Case |
|--------|----------|
| Flow layout | Simple grids |
| Compositional layout | Complex, modern layouts |
| Custom layout | Unique requirements |

### Compositional Layout Components

| Component | Purpose |
|-----------|---------|
| Item | Single cell |
| Group | Horizontal/vertical container |
| Section | Collection section |
| Layout | Combines sections |

### Best Practices

| Practice | Reason |
|----------|--------|
| Use compositional layout | More flexible, modern |
| Diffable data source | Automatic updates |
| Cell registration | Type-safe cell creation |
| Prefetching | Smooth scrolling |

---

## Memory Management

### Common Leak Sources

| Source | Solution |
|--------|----------|
| Closure capturing self | `[weak self]` |
| Delegate strong reference | `weak var delegate` |
| Timer not invalidated | Invalidate in `deinit` |
| Notification observers | Remove in `deinit` |

### Rules

| Rule | Reason |
|------|--------|
| Delegates are `weak` | Prevent retain cycles |
| Closures capture `[weak self]` | Unless explicitly needed |
| Invalidate timers | Timers retain targets |
| Remove NotificationCenter observers | Though auto-removed in modern iOS |

---

## HIG Compliance Checklist

### Touch Targets (HIG)

| Requirement | Minimum |
|-------------|---------|
| Touch area | 44x44 points |
| Spacing between targets | Sufficient to avoid mis-taps |
| Visual affordance | Clear tappable appearance |

### Safe Area

| Area | Handling |
|------|----------|
| Top (notch/Dynamic Island) | Content below safe area |
| Bottom (home indicator) | Interactive elements above |
| Keyboard | Adjust content when keyboard shows |

### Accessibility

| Requirement | Implementation |
|-------------|----------------|
| VoiceOver labels | `accessibilityLabel` |
| Dynamic Type | Use system fonts, support scaling |
| Contrast ratio | 4.5:1 minimum for text |
| Reduce Motion | Respect `UIAccessibility.isReduceMotionEnabled` |

---

## SwiftUI Integration

### Hosting UIKit in SwiftUI

| Wrapper | Use |
|---------|-----|
| `UIViewRepresentable` | UIView in SwiftUI |
| `UIViewControllerRepresentable` | UIViewController in SwiftUI |

### Hosting SwiftUI in UIKit

| Wrapper | Use |
|---------|-----|
| `UIHostingController` | SwiftUI view as view controller |
| Add as child | Embed in UIKit hierarchy |

### Integration Patterns

| Pattern | When |
|---------|------|
| Full SwiftUI screen | New features in UIKit app |
| SwiftUI component in UIKit | Reusable SwiftUI components |
| UIKit component in SwiftUI | UIKit-only features |

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Force unwrapping IBOutlets | Crash if not connected | Verify connections |
| Strong delegate | Memory leak | Use `weak` |
| UI updates on background thread | Crash/undefined | Dispatch to main |
| Not using safe area | Content under notch | Pin to safe area |
| Hardcoded sizes | Doesn't adapt | Use Auto Layout |
| Ignoring Dynamic Type | Accessibility issue | Use system text styles |

---

## Architecture Integration

### MVVM with UIKit

| Component | Role |
|-----------|------|
| ViewController | View layer, binds to ViewModel |
| ViewModel | Business logic, state |
| Binding | Combine or closure-based |

### Coordinator Pattern

| Component | Role |
|-----------|------|
| Coordinator | Navigation logic |
| ViewController | Display only |
| Dependency injection | Pass through coordinator |

---

## Quality Checklist

Before finalizing:

- [ ] All views have proper Auto Layout constraints
- [ ] Touch targets are 44x44 minimum
- [ ] Safe areas respected
- [ ] Memory leaks checked (weak delegates, closures)
- [ ] Accessibility labels set
- [ ] Dynamic Type supported
- [ ] Keyboard handling implemented
- [ ] Lifecycle methods used correctly
- [ ] Cell reuse implemented
- [ ] Dark mode supported

---

## References

- [UIKit Documentation](https://developer.apple.com/documentation/uikit)
- [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/)
- `@HIG/APPLE-HIG.md` - Human Interface Guidelines
- `@conventions/convention.md`
