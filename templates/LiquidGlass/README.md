# Liquid Glass Templates

iOS 26 Liquid Glass style SwiftUI component templates following Apple Human Interface Guidelines.

## Template List

| File | Purpose |
|------|---------|
| `GlassBackground.swift.template` | Base glass background modifier |
| `GlassCard.swift.template` | Glass card modifier and view |
| `GlassButton.swift.template` | Glass buttons (standard + primary) |
| `GlassTextField.swift.template` | Glass text fields (standard + search) |
| `GlassListRow.swift.template` | Glass list row component |
| `GlassNavigationBar.swift.template` | Glass navigation bar |
| `GlassTabBar.swift.template` | Glass tab bar with animations |
| `GlassFloatingButton.swift.template` | Glass floating action button |
| `GlassMessageBubble.swift.template` | Glass chat bubble |
| `GlassAvatar.swift.template` | Glass profile avatar |
| `GlassColors.swift.template` | Color system and animations |

## Design Principles

### Core Characteristics

1. **Translucency**: Semi-transparent backgrounds showing content behind
2. **Blur Effect**: Using `.ultraThinMaterial` ~ `.regularMaterial`
3. **Subtle Borders**: Thin borders (0.5pt) defining edges
4. **Soft Shadows**: Gentle shadows for depth
5. **Rounded Corners**: Smooth corners (12-20pt)
6. **Adaptive Colors**: Automatic light/dark mode adaptation

## Material Guide

| Use Case | Material | Notes |
|----------|----------|-------|
| Cards | `.ultraThinMaterial` | Most transparent |
| Overlays | `.thinMaterial` | Medium transparency |
| Navigation | `.ultraThinMaterial` | Subtle effect |
| Tab Bar | `.ultraThinMaterial` | Consistent with nav |
| Alerts | `.regularMaterial` | More opaque |
| Buttons | `.ultraThinMaterial` | Light touch |

## Animation Presets

| Animation | Use Case |
|-----------|----------|
| `.glassSpring` | Standard component transitions |
| `.glassEase` | Subtle state changes |
| `.glassBounce` | Playful interactions |
| `.glassPress` | Button press feedback |

## Component Selection Guide

| Need | Component | Notes |
|------|-----------|-------|
| Card container | `GlassCardView` / `.glassCard()` | Use modifier for existing views |
| Primary action | `GlassPrimaryButton` | Colored, prominent |
| Secondary action | `GlassButton` | Translucent, subtle |
| Text input | `GlassTextField` | With optional icon |
| Search input | `GlassSearchField` | With clear button |
| List item | `GlassListRow` | Supports leading/trailing |
| Navigation | `GlassNavigationBar` | With action buttons |
| Tab navigation | `GlassTabBar` | With spring animation |
| FAB | `GlassFloatingButton` | For primary screen action |
| Chat | `GlassMessageBubble` | For messaging UI |
| Profile image | `GlassAvatar` | With loading states |

## File Location

Place generated components in:
```
Presentation/Common/Components/{ComponentName}.swift
```

## Usage

The `liquid-glass-ui-builder` agent references these templates when generating Liquid Glass components. Simply request a component type and the agent will use the appropriate template.

```
"Create a glass card for user profile"
"Add a glass navigation bar with back button"
"Build a glass tab bar with 4 tabs"
```
