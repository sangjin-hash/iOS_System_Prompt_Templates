# Apple Human Interface Guidelines (HIG)

> **Reference**: [Apple Developer - Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
>
> **Purpose**: Guidelines for adhering to Apple's design principles when developing iOS apps to pass App Store review and provide excellent user experience

---

## Table of Contents
1. [Design Principles](#design-principles)
2. [iOS Platform Characteristics](#ios-platform-characteristics)
3. [App Architecture](#app-architecture)
4. [Navigation](#navigation)
5. [Modality](#modality)
6. [Interaction](#interaction)
7. [Components](#components)
8. [Visual Design](#visual-design)
9. [Accessibility](#accessibility)
10. [Privacy and Security](#privacy-and-security)
11. [App Store Review Checklist](#app-store-review-checklist)

---

## Design Principles

### 1. Clarity
- **Text Readability**: Text should be easy to read at all sizes
- **Icon Clarity**: Icons should be precise and easy to understand
- **Minimal Decoration**: Functionality should not interfere with content
- **Emphasize Important Functions**: Users should easily find the features they need

### 2. Deference
- **Content First**: UI should highlight content without interfering
- **Use Translucency**: Blur backgrounds to emphasize content
- **Use Whitespace**: Emphasize important elements with appropriate spacing
- **Respect Gestures**: Respond immediately to user gestures

### 3. Depth
- **Visual Hierarchy**: Express hierarchical structure through layers and motion
- **Animation**: Provide smooth animations during screen transitions
- **Shadows and Blur**: Express depth to clarify UI hierarchy

---

## iOS Platform Characteristics

### Screen Sizes and Resolution
- **Support Various Devices**: iPhone (various sizes), iPad, iPhone Pro Max, etc.
- **Safe Area**: Respect safe areas considering notch, Dynamic Island, and home indicator
- **Auto Layout**: Adaptive layouts that respond to various screen sizes
- **Dynamic Type**: Automatically adjust text size according to user settings

### Interface Elements
- **Status Bar**: Top area displaying time, battery, signal strength, etc.
- **Navigation Bar**: Navigation elements at the top of the screen
- **Tab Bar**: Main section switching menu at the bottom of the screen
- **Toolbar**: Tool set for performing actions on the current screen

### Gestures
- **Tap**: Select buttons, controls, items
- **Drag**: Scrolling, panning
- **Swipe**: List item actions, screen transitions
- **Pinch**: Zoom in/out
- **Long Press**: Display context menu
- **Pull to Refresh**: Refresh content

---

## App Architecture

### Launch
- **Fast Launch**: App should launch as quickly as possible
- **Launch Screen**: Use static image similar to app's first screen
  - No ads, branding, or splash screens
  - Minimize text and logos
- **Minimize Initial Setup**: Require only essential settings, rest can come later

### Onboarding
- **Provide Immediate Value**: Make it usable without tutorials
- **Optional Tutorial**: Briefly explain only complex features
- **Interactive Learning**: Encourage learning during actual use rather than static screens
- **Skip Option**: Users should be able to skip onboarding

### Loading
- **Show Progress**: Display progress for long operations
- **Activity Indicator**: For unpredictable time operations
- **Progress Bar**: For predictable time operations
- **Load Content First**: Display important content first

### Update Requests
- **Minimize Forced Updates**: Force only for critical security issues
- **Explain Update Benefits**: Inform about new features or improvements
- **Remind Later Option**: Allow users to choose

---

## Navigation

### Hierarchical Navigation
- **Settings App Style**: Drill down from parent to child screens
- **Use Navigation Bar**: Return to previous screen with Back button
- **Clear Titles**: Display each screen's title clearly
- **Use Large Title**: Use large title on top-level screens

### Flat Navigation
- **Use Tab Bar**: Quick switching between main sections
- **Recommend 3-5 Tabs**: Too many tabs cause confusion
- **Show Current Location**: Clearly indicate selected tab
- **Consistent Tab Order**: Maintain tab order throughout app

### Content-Driven Navigation
- **Games or Media Apps**: Free movement centered on content
- **Provide Clear Path**: Users should understand their current location
- **Back Function**: Provide a way to return to previous state

### Best Practices
- **Predictable Behavior**: Follow standard navigation patterns
- **Clear Back Button**: Display previous screen title
- **Page Control**: Display multiple screens at same level (pagination)
- **Segmented Control**: Switch closely related content

---

## Modality

### When to Use Modals
- **Convey Important Information**: When user attention is required
- **Complex Tasks**: Tasks requiring multiple steps
- **Require Clear Choice**: When user decision is needed

### Modal Types

#### 1. Alerts
- **Convey Important Messages**: Errors, warnings, confirmation requests
- **Concise Title and Message**: Clear and easy to understand
- **1-2 Buttons**: Avoid too many options
- **Clarify Destructive Actions**: Emphasize in red

#### 2. Action Sheets
- **Provide Multiple Options**: Options related to current task
- **Include Cancel Button**: Allow users to exit
- **Use Popover on iPad**: Instead of Action Sheet

#### 3. Sheets (Modal Sheets)
- **Full Screen Tasks**: Information entry, settings, etc.
- **Clear Done/Cancel Buttons**: Place at top
- **Confirm Changes**: Display confirmation message when canceling
- **Card Style**: Default modal presentation style

#### 4. Popovers (iPad)
- **Additional Information or Options**: Maintain context
- **Close with External Tap**: Users should easily close
- **Connect with Arrow**: Clearly show where it appeared from

### Modal Usage Principles
- **No Abuse**: Use only when necessary
- **Clear Closing Method**: Users should know how to close
- **Minimize Workflow Interruption**: Handle inline when possible
- **Close with Swipe**: Support swipe down to close modals

---

## Interaction

### Touch and Gestures
- **44x44 Point Touch Area**: Minimum touchable area
- **Follow Standard Gestures**: Use gestures familiar to users
- **Teach Custom Gestures**: Explain usage for non-standard gestures
- **Avoid Gesture Conflicts**: Don't conflict with system gestures

### Feedback
- **Visual Feedback**: Button state changes, highlights
- **Haptic Feedback**: Provide physical response with Haptic Feedback
- **Audio Feedback**: Add sound to important actions (optional)
- **Immediate Response**: Respond immediately to user input

### Animation
- **Purposeful Animation**: Animations that aid visual continuity and understanding
- **Appropriate Speed**: Not too fast or slow (0.2-0.4 seconds)
- **Natural Movement**: Natural curves like ease-in-out
- **Consider Reduce Motion**: Respect animation reduction option in settings

### Drag and Drop
- **Clear Drag Targets**: Make it clear what can be dragged
- **Visual Feedback**: Highlight targets during drag
- **Show Drop Zones**: Indicate where drops are possible
- **Handle Failures**: Animate invalid drops back to original position

---

## Components

### Buttons

#### System Buttons
- **Clear Labels**: Clear actions starting with verbs
- **Appropriate Size**: Minimum 44x44 points
- **State Display**: Distinguish Normal, Highlighted, Disabled states

#### Text Buttons
- **Simple Actions**: Mainly for navigation or simple tasks
- **Use SF Symbols**: Combination of icons and text

#### Filled Buttons
- **Primary Actions**: Most important action on screen
- **Sufficient Margins**: Distinguish from surrounding elements
- **One Primary Button**: Recommend one Filled Button per screen

#### Floating Action Button (FAB)
- **Not Recommended on iOS**: Android pattern, use tab bar or toolbar on iOS

### Lists and Tables

#### List Styles
- **Plain**: Default list style
- **Grouped**: Lists grouped into sections
- **Inset Grouped**: Group lists with margins
- **Sidebar**: Lists for sidebar on iPad

#### List Items
- **Clear Hierarchy**: Distinguish title, subtitle, detail information
- **Disclosure Indicator**: Arrow to show more information
- **Accessory Views**: Checkmarks, info buttons, etc.
- **Swipe Actions**: Provide actions like delete, edit
- **Contextual Menus**: More options with Long Press

#### Performance
- **Cell Reuse**: Optimize scroll performance
- **Asynchronous Image Loading**: Network images in background
- **Appropriate Cell Height**: Fixed height is better for performance than automatic

### Text Fields and Text Views

#### Text Field
- **Clear Placeholder**: Provide input example
- **Clear Button**: Quickly clear input
- **Appropriate Keyboard Type**: Email, Number, URL, etc.
- **Customize Return Key**: Next, Done, Search, etc.
- **Input Validation**: Real-time or on submission validation

#### Text View
- **Multi-line Input**: For long text input
- **Scrollable**: Scroll when content is abundant
- **Auto Height Adjustment**: Change height according to content (optional)

#### Security
- **Password Field**: Enable Secure Text Entry
- **Auto-complete**: Set appropriate Content Type
- **Two-Factor Authentication**: Support SMS auto-fill

### Search

#### Search Bar
- **Clear Location**: Mainly placed in Navigation Bar
- **Placeholder**: "Search" or "Search OO"
- **Cancel Button**: Search cancellation option
- **Scope Bar**: Search scope filtering (optional)

#### Search Experience
- **Show Results Immediately**: Real-time search while typing
- **Recent Searches**: Display previous search terms
- **Suggested Searches**: Popular or recommended search terms
- **Handle No Results**: Suggest alternatives

### Pickers

#### Date Picker
- **Inline Style**: Default date picker style
- **Wheels Style**: Drum roll style (legacy)
- **Compact Style**: Space-saving type

#### Picker View
- **Multiple Components**: Multiple selection wheels
- **Clear Selection**: Emphasize central selection area

### Switches and Toggles
- **On/Off State**: Enable/disable settings
- **Immediate Application**: Settings change immediately on toggle
- **Provide Label**: Make it clear what is being turned on/off

### Sliders
- **Continuous Values**: Volume, brightness, etc.
- **Show Min/Max**: Clarify range
- **Real-time Feedback**: Reflect value during slider movement

### Steppers
- **Discrete Value Adjustment**: Increase/decrease value with +/- buttons
- **Display Current Value**: Show current value numerically

### Progress Indicators

#### Progress Bar
- **Determinate Progress**: Tasks with predictable time
- **Show 0-100%**: Clear progress rate

#### Activity Indicator
- **Indeterminate Progress**: Tasks with unpredictable time
- **Appropriate Size**: Small, Medium, Large

#### Progress Views
- **Background Tasks**: Downloads, uploads, etc.
- **Cancel Option**: Provide cancel button when possible

### Segmented Controls
- **Closely Related Options**: 2-5 segments
- **Equal Width**: Same width when possible
- **Clear Labels**: Short and clear text

### Tab Bars
- **Main Sections**: Top-level navigation of the app
- **3-5 Tabs**: Use More tab if too many
- **Icons and Labels**: Recommend providing both
- **Show Current Tab**: Distinguish by color

### Toolbars
- **Current Screen Actions**: Edit, share, delete, etc.
- **Icon Buttons**: Prefer icons over text
- **Appropriate Spacing**: Sufficient margins between buttons

### Navigation Bars
- **Screen Title**: Display current screen
- **Back Button**: Return to previous screen
- **Action Buttons**: Primary actions (Done, Edit, Add, etc.)
- **Large Title**: Use on top-level screens

### Alerts and Action Sheets
- **Concise Title**: Summarize in one line
- **Descriptive Message**: Optional, only when necessary
- **Clear Buttons**: Labels with clear actions
- **Destructive Actions**: Emphasize in red

### Sheets and Modals
- **Clear Purpose**: Make it clear why modal appeared
- **Done/Cancel Buttons**: Place at top
- **Card Style**: Default alert presentation style
- **Close with Swipe**: Support swipe down

### Context Menus
- **Long Press**: Long press item to show menu
- **Preview**: Provide content preview when possible
- **Related Actions**: Only actions related to current item
- **Include Icons**: Visual cues with SF Symbols

### Pull-Down Menus
- **Button Menu**: Press button to show menu
- **Hierarchical Structure**: Support submenus
- **Checkmarks**: Indicate selected items

---

## Visual Design

### Color

#### System Colors
- **Dynamic Colors**: Automatically adapt to Light/Dark Mode
- **Semantic Colors**: Colors according to meaning (Label, Background, Fill, etc.)
- **Tint Color**: App's primary color, use consistently

#### Color Usage Principles
- **Clear Hierarchy**: Distinguish importance by color
- **Consider Accessibility**: Sufficient contrast ratio (WCAG AA or higher)
- **Consider Color Blindness**: Don't convey information by color alone
- **Brand Color**: Color representing app identity

#### Light/Dark Mode
- **Automatic Switching**: Switch automatically according to system settings
- **Use Semantic Colors**: Automatically adapt without manual changes
- **Asset Catalog**: Provide Light/Dark versions for images too
- **Test**: Test in both modes

### Typography

#### San Francisco Font
- **System Font**: SF Pro (iOS), SF Compact (watchOS)
- **Dynamic Type**: Automatically adjust size according to user settings
- **Use Weight**: Ultralight to Black (9 levels)

#### Text Styles
- **Large Title**: 34pt, Bold
- **Title 1**: 28pt, Regular
- **Title 2**: 22pt, Regular
- **Title 3**: 20pt, Regular
- **Headline**: 17pt, Semibold
- **Body**: 17pt, Regular (default text)
- **Callout**: 16pt, Regular
- **Subheadline**: 15pt, Regular
- **Footnote**: 13pt, Regular
- **Caption 1**: 12pt, Regular
- **Caption 2**: 11pt, Regular

#### Typography Principles
- **Readability First**: Sufficient size and spacing
- **Consistency**: Same style for text with same purpose
- **Hierarchy**: Express importance through size and weight
- **Support Dynamic Type**: Respect user settings
- **Line Spacing**: Appropriate line height
- **Alignment**: Left alignment default, center alignment limited

### Icons and Images

#### SF Symbols
- **System Icons**: Over 4,000 vector icons
- **Consistent Style**: Harmonizes with San Francisco font
- **Auto Size Adjustment**: Links with Dynamic Type
- **Weight and Scale**: Various thickness and size variations
- **Color**: Support monochrome, multicolor, hierarchical colors
- **Custom Symbols**: Can create app-specific symbols

#### App Icon
- **1024x1024**: High resolution for App Store
- **No Rounded Corners**: System applies automatically
- **Gradients and Shadows**: Use appropriately
- **Brand Identity**: Design representing the app
- **Simple and Clear**: Not complex, easy to recognize
- **Fill Background**: No transparent background

#### Image Usage
- **Resolution**: Provide @1x, @2x, @3x
- **Asset Catalog**: Use image management tool
- **Compression**: Optimize capacity with appropriate compression
- **LazyLoading**: Load when needed
- **Placeholder**: Display during loading

#### Template Images
- **Apply Tint Color**: Monochrome icons
- **Rendering Mode**: Template vs Original

### Layout

#### Safe Area
- **Notch/Dynamic Island**: Top safe area
- **Home Indicator**: Bottom safe area
- **Consider Margins**: Place important content inside Safe Area

#### Auto Layout
- **Adaptive Layout**: Respond to various screen sizes
- **Constraint-based**: Clear constraints
- **Size Classes**: Regular/Compact combinations
- **Dynamic Height**: Adjust height according to content

#### Grid and Alignment
- **Consistent Spacing**: Recommend 8pt or 4pt multiples
- **Margins**: Minimum 16pt left/right margins
- **Alignment**: Align elements for clean design

#### Responsive Design
- **Portrait/Landscape**: Support both orientations
- **iPad Multitasking**: Support Split View, Slide Over
- **Dynamic Type**: Respond to text size changes

### Animation and Motion

#### Principles
- **Purposeful**: Only meaningful animations
- **Natural**: Movement following physical laws
- **Fast Speed**: Recommend 0.2-0.4 seconds
- **Consider Reduce Motion**: Respect Reduce Motion setting

#### Animation Types
- **Screen Transitions**: Push, Present, Dismiss
- **Element Changes**: Fade, Scale, Move
- **Feedback**: Button Highlight, Haptics
- **Progress State**: Spinner, Progress Bar

---

## Accessibility

### VoiceOver
- **Labels**: Clear labels for all UI elements
- **Hints**: Provide additional explanation when necessary
- **Order**: Logical focus order
- **Grouping**: Group related elements
- **Custom Actions**: Provide additional actions with Swipe

### Dynamic Type
- **Scalable**: Respond to text size changes
- **Layout Adjustment**: Don't break with large text
- **Scroll Area**: Scroll for truncated text
- **Minimum Size**: Maintain minimum size for important text

### Color and Contrast
- **Contrast Ratio**: Minimum 4.5:1 (text), 3:1 (UI elements)
- **Consider Color Blindness**: Don't convey information by color alone
- **Dark Mode**: Maintain sufficient contrast

### Touch Area
- **Minimum Size**: 44x44 points
- **Sufficient Spacing**: Margins between buttons
- **Clear Boundaries**: Clarify touchable area

### Motion
- **Reduce Motion**: Respect animation reduction setting
- **Alternative Effects**: Fade instead of animation

### Accessibility Testing
- **Accessibility Inspector**: Use Xcode tool
- **VoiceOver Test**: Actually test with VoiceOver
- **Dynamic Type Test**: Test with various sizes
- **Check Color Contrast**: Use contrast ratio checking tool

---

## Privacy and Security

### Permission Requests

#### Location
- **Clear Reason**: Explain why it's needed
- **Minimum Permission**: When In Use vs Always
- **Permission Prompt**: Request at necessary moment
- **Info.plist Description**: `NSLocationWhenInUseUsageDescription`

#### Camera and Microphone
- **Explain Purpose**: Present clear reason
- **Info.plist Description**: `NSCameraUsageDescription`, `NSMicrophoneUsageDescription`

#### Photo Library
- **Limited Access**: Support Limited Photo Access
- **Info.plist Description**: `NSPhotoLibraryUsageDescription`

#### Contacts, Calendar, Reminders
- **Only When Necessary**: Only for core features
- **Info.plist Description**: Each Usage Description

#### Permission Request Principles
- **Timely Request**: Right before feature use
- **Provide Context**: Explain why it's needed first
- **Handle Denial**: Provide alternatives when permission denied
- **Settings Guidance**: Guide how to change permissions

### Data Protection
- **Encryption**: Encrypt sensitive data
- **Keychain**: Store passwords, tokens
- **HTTPS**: Encrypt network communication
- **App Transport Security**: Comply with ATS

### Authentication and Authorization
- **Secure Authentication**: OAuth, Sign in with Apple
- **Biometric Authentication**: Use Face ID, Touch ID
- **Two-Factor Authentication**: Support 2FA

### Privacy Labels
- **App Store**: Specify collected data
- **Transparency**: Provide accurate information
- **Update**: Update when policy changes

---

## App Store Review Checklist

### Essential Requirements

#### 1. App Completeness
- [ ] App works without crashes or bugs
- [ ] All links work
- [ ] No placeholder content
- [ ] Remove demo mode or test content

#### 2. Design
- [ ] Follow HIG principles
- [ ] Use iOS standard UI components
- [ ] Appropriate navigation
- [ ] Consistent design

#### 3. Functionality
- [ ] Provide clear value
- [ ] No advertising apps
- [ ] Avoid duplicate apps
- [ ] Provide complete functionality

#### 4. Content
- [ ] Appropriate content (no inappropriate content)
- [ ] Monitor user-generated content
- [ ] Respect copyright

#### 5. Legal Requirements
- [ ] Privacy policy
- [ ] Terms of service
- [ ] License information

### Technical Requirements

#### Launch Screen
- [ ] Use static Launch Screen
- [ ] No ads or branding
- [ ] Design similar to first screen

#### Icon
- [ ] 1024x1024 size
- [ ] No transparent background
- [ ] No rounded corners (system applies)

#### Screen Sizes
- [ ] Support all iPhone sizes
- [ ] Follow Safe Area
- [ ] Support Portrait and Landscape (when necessary)

#### Performance
- [ ] Fast launch time
- [ ] Memory efficient
- [ ] Battery efficient
- [ ] Smooth scrolling

### Accessibility Requirements
- [ ] VoiceOver support
- [ ] Dynamic Type support
- [ ] Sufficient color contrast
- [ ] Touch area size (44x44pt)
- [ ] Consider Reduce Motion

### Permission Requirements
- [ ] Write Info.plist Usage Description
- [ ] Request permissions timely
- [ ] Provide clear explanation
- [ ] Provide alternatives on denial

### Security Requirements
- [ ] Use HTTPS
- [ ] Encrypt sensitive data
- [ ] Use Keychain
- [ ] Secure authentication

### App Store Submission
- [ ] Accurate app description
- [ ] Provide screenshots
- [ ] Select category
- [ ] Set age rating
- [ ] Write privacy labels
- [ ] Provide test account (when necessary)

---

## Additional Resources

### Official Documentation
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)
- [iOS Design Resources](https://developer.apple.com/design/resources/)

### Design Tools
- **SF Symbols App**: SF Symbols browser
- **Accessibility Inspector**: Xcode accessibility testing tool
- **Sketch/Figma**: UI design tools
- **Xcode Previews**: SwiftUI real-time preview

### Learning Resources
- Apple Developer Documentation
- WWDC Videos
- Apple Design Resources
- iOS Developer Community

---

## Update History
- **2025-01**: Initial document creation
- Reflecting HIG content based on iOS 18

---

## Notes

This document is a guide for adhering to Apple HIG when developing iOS apps. Following these guidelines is important to pass app review and provide excellent user experience.

**Important**: This document is a summary, and for detailed information, please refer to the official Apple HIG documentation. Apple HIG is continuously updated, so it's recommended to check the latest version.

**Guide for Agents**: When developing iOS screens, follow the content of this document to ensure:
1. Use standard iOS UI components
2. Consider Safe Area
3. Support Dynamic Type
4. Follow accessibility
5. Support Light/Dark Mode
6. Appropriate navigation patterns
7. Consistent design language
8. Clear user feedback
9. Performance optimization
10. Privacy and security compliance

These must be considered when developing.
