---
name: liquid-glass-ui-builder
description: iOS 26 Liquid Glass style UI component generator. Creates glassmorphism effects and Apple HIG compliant SwiftUI components.
keywords: SwiftUI, Liquid Glass, Glassmorphism, iOS 26, UI, components, Apple HIG, design
---

# Liquid Glass UI Builder Agent

## Purpose
Generates iOS 26 Liquid Glass style SwiftUI components. Implements glassmorphism effects with blur, transparency, and modern UI following Apple Human Interface Guidelines.

**Use this agent when**:
- Creating Liquid Glass style components
- Converting existing components to glassmorphism style
- Building consistent UI system

## Prerequisites

**Required Context**:
- Component type (Card, Button, TextField, List, etc.)
- Component purpose and content
- Light/Dark mode support requirements

**References**:
- Apple Human Interface Guidelines
- `@conventions/convention-EN.md` - Coding conventions

---

## Liquid Glass Design Principles

### Core Characteristics

1. **Translucency**: Semi-transparent backgrounds showing content behind
2. **Blur Effect**: Using `.ultraThinMaterial` ~ `.regularMaterial`
3. **Subtle Borders**: Thin borders defining edges
4. **Soft Shadows**: Gentle shadows for depth
5. **Rounded Corners**: Smooth corners (12-20pt)
6. **Adaptive Colors**: Auto light/dark mode adaptation

---

## Base Styles

### Glass Background Modifier

```swift
import SwiftUI

struct GlassBackground: ViewModifier {
    var cornerRadius: CGFloat = 16
    var material: Material = .ultraThinMaterial

    func body(content: Content) -> some View {
        content
            .background(material, in: RoundedRectangle(cornerRadius: cornerRadius))
            .overlay {
                RoundedRectangle(cornerRadius: cornerRadius)
                    .stroke(.white.opacity(0.2), lineWidth: 0.5)
            }
            .shadow(color: .black.opacity(0.1), radius: 10, x: 0, y: 5)
    }
}

extension View {
    func glassBackground(
        cornerRadius: CGFloat = 16,
        material: Material = .ultraThinMaterial
    ) -> some View {
        modifier(GlassBackground(cornerRadius: cornerRadius, material: material))
    }
}
```

### Glass Card Modifier

```swift
struct GlassCard: ViewModifier {
    var padding: CGFloat = 16
    var cornerRadius: CGFloat = 20

    func body(content: Content) -> some View {
        content
            .padding(padding)
            .glassBackground(cornerRadius: cornerRadius, material: .thinMaterial)
    }
}

extension View {
    func glassCard(padding: CGFloat = 16, cornerRadius: CGFloat = 20) -> some View {
        modifier(GlassCard(padding: padding, cornerRadius: cornerRadius))
    }
}
```

---

## Component Templates

### 1. Glass Card

```swift
struct GlassCardView<Content: View>: View {
    let content: () -> Content

    init(@ViewBuilder content: @escaping () -> Content) {
        self.content = content
    }

    var body: some View {
        content()
            .padding(16)
            .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 20))
            .overlay {
                RoundedRectangle(cornerRadius: 20)
                    .stroke(
                        LinearGradient(
                            colors: [.white.opacity(0.3), .white.opacity(0.1)],
                            startPoint: .topLeading,
                            endPoint: .bottomTrailing
                        ),
                        lineWidth: 1
                    )
            }
            .shadow(color: .black.opacity(0.08), radius: 12, x: 0, y: 6)
    }
}

// Usage
GlassCardView {
    VStack(alignment: .leading, spacing: 8) {
        Text("Title")
            .font(.headline)
        Text("Description")
            .font(.subheadline)
            .foregroundStyle(.secondary)
    }
}
```

### 2. Glass Button

```swift
struct GlassButton: View {
    let title: String
    let icon: String?
    let action: () -> Void

    @State private var isPressed = false

    init(_ title: String, icon: String? = nil, action: @escaping () -> Void) {
        self.title = title
        self.icon = icon
        self.action = action
    }

    var body: some View {
        Button(action: action) {
            HStack(spacing: 8) {
                if let icon {
                    Image(systemName: icon)
                }
                Text(title)
                    .fontWeight(.medium)
            }
            .padding(.horizontal, 20)
            .padding(.vertical, 12)
            .background(.ultraThinMaterial, in: Capsule())
            .overlay {
                Capsule()
                    .stroke(.white.opacity(0.25), lineWidth: 0.5)
            }
            .shadow(color: .black.opacity(0.1), radius: 8, x: 0, y: 4)
            .scaleEffect(isPressed ? 0.97 : 1.0)
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: .infinity, pressing: { pressing in
            withAnimation(.easeInOut(duration: 0.15)) {
                isPressed = pressing
            }
        }, perform: {})
    }
}

// Primary variant
struct GlassPrimaryButton: View {
    let title: String
    let action: () -> Void

    var body: some View {
        Button(action: action) {
            Text(title)
                .fontWeight(.semibold)
                .foregroundStyle(.white)
                .padding(.horizontal, 24)
                .padding(.vertical, 14)
                .background(
                    LinearGradient(
                        colors: [.blue, .blue.opacity(0.8)],
                        startPoint: .top,
                        endPoint: .bottom
                    ),
                    in: Capsule()
                )
                .overlay {
                    Capsule()
                        .stroke(.white.opacity(0.3), lineWidth: 0.5)
                }
                .shadow(color: .blue.opacity(0.3), radius: 10, x: 0, y: 5)
        }
        .buttonStyle(.plain)
    }
}
```

### 3. Glass TextField

```swift
struct GlassTextField: View {
    let placeholder: String
    @Binding var text: String
    var icon: String? = nil

    @FocusState private var isFocused: Bool

    var body: some View {
        HStack(spacing: 12) {
            if let icon {
                Image(systemName: icon)
                    .foregroundStyle(.secondary)
                    .frame(width: 20)
            }

            TextField(placeholder, text: $text)
                .focused($isFocused)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 14)
        .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 12))
        .overlay {
            RoundedRectangle(cornerRadius: 12)
                .stroke(
                    isFocused ? Color.accentColor.opacity(0.5) : Color.white.opacity(0.2),
                    lineWidth: isFocused ? 1.5 : 0.5
                )
        }
        .animation(.easeInOut(duration: 0.2), value: isFocused)
    }
}

// Search variant
struct GlassSearchField: View {
    @Binding var text: String
    var placeholder: String = "Search"
    var onSubmit: (() -> Void)? = nil

    var body: some View {
        HStack(spacing: 10) {
            Image(systemName: "magnifyingglass")
                .foregroundStyle(.secondary)

            TextField(placeholder, text: $text)
                .onSubmit { onSubmit?() }

            if !text.isEmpty {
                Button {
                    text = ""
                } label: {
                    Image(systemName: "xmark.circle.fill")
                        .foregroundStyle(.secondary)
                }
            }
        }
        .padding(.horizontal, 14)
        .padding(.vertical, 10)
        .background(.ultraThinMaterial, in: Capsule())
        .overlay {
            Capsule()
                .stroke(.white.opacity(0.15), lineWidth: 0.5)
        }
    }
}
```

### 4. Glass List Row

```swift
struct GlassListRow<Leading: View, Trailing: View>: View {
    let title: String
    var subtitle: String? = nil
    let leading: () -> Leading
    let trailing: () -> Trailing
    var action: (() -> Void)? = nil

    init(
        title: String,
        subtitle: String? = nil,
        @ViewBuilder leading: @escaping () -> Leading,
        @ViewBuilder trailing: @escaping () -> Trailing,
        action: (() -> Void)? = nil
    ) {
        self.title = title
        self.subtitle = subtitle
        self.leading = leading
        self.trailing = trailing
        self.action = action
    }

    var body: some View {
        Button {
            action?()
        } label: {
            HStack(spacing: 12) {
                leading()

                VStack(alignment: .leading, spacing: 2) {
                    Text(title)
                        .font(.body)
                        .foregroundStyle(.primary)

                    if let subtitle {
                        Text(subtitle)
                            .font(.subheadline)
                            .foregroundStyle(.secondary)
                    }
                }

                Spacer()

                trailing()
            }
            .padding(.horizontal, 16)
            .padding(.vertical, 12)
            .background(.ultraThinMaterial, in: RoundedRectangle(cornerRadius: 14))
            .overlay {
                RoundedRectangle(cornerRadius: 14)
                    .stroke(.white.opacity(0.15), lineWidth: 0.5)
            }
        }
        .buttonStyle(.plain)
    }
}

// Convenience initializers
extension GlassListRow where Leading == EmptyView {
    init(
        title: String,
        subtitle: String? = nil,
        @ViewBuilder trailing: @escaping () -> Trailing,
        action: (() -> Void)? = nil
    ) {
        self.init(title: title, subtitle: subtitle, leading: { EmptyView() }, trailing: trailing, action: action)
    }
}

extension GlassListRow where Trailing == EmptyView {
    init(
        title: String,
        subtitle: String? = nil,
        @ViewBuilder leading: @escaping () -> Leading,
        action: (() -> Void)? = nil
    ) {
        self.init(title: title, subtitle: subtitle, leading: leading, trailing: { EmptyView() }, action: action)
    }
}
```

### 5. Glass Navigation Bar

```swift
struct GlassNavigationBar<Leading: View, Trailing: View>: View {
    let title: String
    let leading: () -> Leading
    let trailing: () -> Trailing

    init(
        title: String,
        @ViewBuilder leading: @escaping () -> Leading = { EmptyView() },
        @ViewBuilder trailing: @escaping () -> Trailing = { EmptyView() }
    ) {
        self.title = title
        self.leading = leading
        self.trailing = trailing
    }

    var body: some View {
        HStack {
            leading()
                .frame(width: 60, alignment: .leading)

            Spacer()

            Text(title)
                .font(.headline)
                .fontWeight(.semibold)

            Spacer()

            trailing()
                .frame(width: 60, alignment: .trailing)
        }
        .padding(.horizontal, 16)
        .padding(.vertical, 12)
        .background(.ultraThinMaterial)
        .overlay(alignment: .bottom) {
            Rectangle()
                .fill(.white.opacity(0.1))
                .frame(height: 0.5)
        }
    }
}
```

### 6. Glass Tab Bar

```swift
struct GlassTabBar: View {
    @Binding var selectedTab: Int
    let tabs: [(icon: String, title: String)]

    var body: some View {
        HStack(spacing: 0) {
            ForEach(Array(tabs.enumerated()), id: \.offset) { index, tab in
                Button {
                    withAnimation(.spring(response: 0.3, dampingFraction: 0.7)) {
                        selectedTab = index
                    }
                } label: {
                    VStack(spacing: 4) {
                        Image(systemName: selectedTab == index ? "\(tab.icon).fill" : tab.icon)
                            .font(.system(size: 22))
                            .symbolEffect(.bounce, value: selectedTab == index)

                        Text(tab.title)
                            .font(.caption2)
                    }
                    .foregroundStyle(selectedTab == index ? .primary : .secondary)
                    .frame(maxWidth: .infinity)
                    .padding(.vertical, 8)
                }
                .buttonStyle(.plain)
            }
        }
        .padding(.horizontal, 8)
        .padding(.top, 8)
        .padding(.bottom, 4)
        .background(.ultraThinMaterial)
        .overlay(alignment: .top) {
            Rectangle()
                .fill(.white.opacity(0.1))
                .frame(height: 0.5)
        }
    }
}
```

### 7. Glass Floating Action Button

```swift
struct GlassFloatingButton: View {
    let icon: String
    let action: () -> Void

    @State private var isPressed = false

    var body: some View {
        Button(action: action) {
            Image(systemName: icon)
                .font(.system(size: 22, weight: .medium))
                .foregroundStyle(.primary)
                .frame(width: 56, height: 56)
                .background(.thinMaterial, in: Circle())
                .overlay {
                    Circle()
                        .stroke(
                            LinearGradient(
                                colors: [.white.opacity(0.4), .white.opacity(0.1)],
                                startPoint: .topLeading,
                                endPoint: .bottomTrailing
                            ),
                            lineWidth: 1
                        )
                }
                .shadow(color: .black.opacity(0.15), radius: 12, x: 0, y: 6)
                .scaleEffect(isPressed ? 0.92 : 1.0)
        }
        .buttonStyle(.plain)
        .onLongPressGesture(minimumDuration: .infinity, pressing: { pressing in
            withAnimation(.easeInOut(duration: 0.15)) {
                isPressed = pressing
            }
        }, perform: {})
    }
}
```

### 8. Glass Message Bubble

```swift
struct GlassMessageBubble: View {
    let message: String
    let isFromMe: Bool
    let time: String

    var body: some View {
        HStack {
            if isFromMe { Spacer(minLength: 60) }

            VStack(alignment: isFromMe ? .trailing : .leading, spacing: 4) {
                Text(message)
                    .padding(.horizontal, 14)
                    .padding(.vertical, 10)
                    .background(
                        isFromMe
                            ? AnyShapeStyle(LinearGradient(
                                colors: [.green, .green.opacity(0.85)],
                                startPoint: .topLeading,
                                endPoint: .bottomTrailing
                            ))
                            : AnyShapeStyle(.ultraThinMaterial)
                        ,
                        in: BubbleShape(isFromMe: isFromMe)
                    )
                    .foregroundStyle(isFromMe ? .white : .primary)
                    .overlay {
                        BubbleShape(isFromMe: isFromMe)
                            .stroke(.white.opacity(isFromMe ? 0.2 : 0.15), lineWidth: 0.5)
                    }

                Text(time)
                    .font(.caption2)
                    .foregroundStyle(.secondary)
            }

            if !isFromMe { Spacer(minLength: 60) }
        }
    }
}

struct BubbleShape: Shape {
    let isFromMe: Bool

    func path(in rect: CGRect) -> Path {
        let radius: CGFloat = 18
        let tailSize: CGFloat = 6

        var path = Path()

        if isFromMe {
            path.addRoundedRect(
                in: CGRect(x: 0, y: 0, width: rect.width - tailSize, height: rect.height),
                cornerSize: CGSize(width: radius, height: radius)
            )
        } else {
            path.addRoundedRect(
                in: CGRect(x: tailSize, y: 0, width: rect.width - tailSize, height: rect.height),
                cornerSize: CGSize(width: radius, height: radius)
            )
        }

        return path
    }
}
```

### 9. Glass Badge

```swift
struct GlassBadge: View {
    let count: Int

    var body: some View {
        if count > 0 {
            Text(count > 99 ? "99+" : "\(count)")
                .font(.caption2)
                .fontWeight(.bold)
                .foregroundStyle(.white)
                .padding(.horizontal, count > 9 ? 6 : 0)
                .frame(minWidth: 18, minHeight: 18)
                .background(.red, in: Capsule())
                .overlay {
                    Capsule()
                        .stroke(.white.opacity(0.3), lineWidth: 0.5)
                }
        }
    }
}
```

### 10. Glass Profile Avatar

```swift
struct GlassAvatar: View {
    let imageURL: URL?
    let size: CGFloat
    var placeholder: String = "person.fill"

    var body: some View {
        Group {
            if let imageURL {
                AsyncImage(url: imageURL) { phase in
                    switch phase {
                    case .success(let image):
                        image
                            .resizable()
                            .scaledToFill()
                    case .failure:
                        placeholderView
                    case .empty:
                        ProgressView()
                    @unknown default:
                        placeholderView
                    }
                }
            } else {
                placeholderView
            }
        }
        .frame(width: size, height: size)
        .clipShape(Circle())
        .overlay {
            Circle()
                .stroke(
                    LinearGradient(
                        colors: [.white.opacity(0.4), .white.opacity(0.1)],
                        startPoint: .topLeading,
                        endPoint: .bottomTrailing
                    ),
                    lineWidth: 2
                )
        }
        .shadow(color: .black.opacity(0.1), radius: 6, x: 0, y: 3)
    }

    private var placeholderView: some View {
        Image(systemName: placeholder)
            .font(.system(size: size * 0.4))
            .foregroundStyle(.secondary)
            .frame(width: size, height: size)
            .background(.ultraThinMaterial, in: Circle())
    }
}
```

---

## Color System

### Semantic Colors

```swift
extension Color {
    // Glass-friendly colors
    static let glassBackground = Color.primary.opacity(0.05)
    static let glassBorder = Color.white.opacity(0.2)
    static let glassShadow = Color.black.opacity(0.1)

    // Accent variations
    static let glassAccent = Color.accentColor.opacity(0.8)
    static let glassAccentLight = Color.accentColor.opacity(0.3)
}
```

### Material Guide

| Use Case | Material | Notes |
|----------|----------|-------|
| Cards | `.ultraThinMaterial` | Most transparent |
| Overlays | `.thinMaterial` | Medium transparency |
| Navigation | `.ultraThinMaterial` | Subtle effect |
| Tab Bar | `.ultraThinMaterial` | Consistent with nav |
| Alerts | `.regularMaterial` | More opaque |
| Buttons | `.ultraThinMaterial` | Light touch |

---

## Animation Presets

```swift
extension Animation {
    static let glassSpring = Animation.spring(response: 0.35, dampingFraction: 0.7)
    static let glassEase = Animation.easeInOut(duration: 0.25)
    static let glassBounce = Animation.spring(response: 0.4, dampingFraction: 0.6)
}
```

---

## Output Format

When generating components, output in this format:

```markdown
## Generated: {ComponentName}

### File
`Presentation/Common/Components/{ComponentName}.swift`

### Preview
[Component preview code]

### Usage Example
```swift
// Usage example
```

### Customization Options
- `option1`: Description
- `option2`: Description

### Accessibility
- VoiceOver support: Yes/No
- Dynamic Type support: Yes/No
```

---

## References

- Apple Human Interface Guidelines - Materials
- iOS 26 Design System
- `@conventions/convention-EN.md` - SwiftUI conventions
