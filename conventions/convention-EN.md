# Swift Code Style Guide (English)

This is a concise style guide for swift-code-reviewer agent reference.
For detailed explanations in Korean, see `convention-KR.md`.

---

## 1. Boolean Naming

### Rules
- **@State/@Binding**: `is+PastParticiple` (e.g., `isPresented`)
- **Function parameters**: `PastParticiple` without `is` (e.g., `animated`, `selected`)
- **Settings/Options**: `ThirdPersonSingular` (e.g., `showsProfileImage`, `enablesDarkTheme`)
- **Other cases**: `is+PastParticiple` (e.g., `isCompleted`, `isOutdated`)

### Examples
```swift
// ✅ Correct
@State private var isAddingNewEvent = false
func present(_ vc: UIViewController, animated: Bool)
var showsProfileImage: Bool

// ❌ Wrong
@State private var addingNewEvent = false
func present(_ vc: UIViewController, isAnimated: Bool)
var showProfile: Bool  // for settings
```

---

## 2. Function Naming

### Rules
- **Actions**: Verb form (e.g., `remove`, `sort`, `fetch`)
- **Value retrieval**: Noun form (e.g., `cellForRow`, `user(for:)`)
- **Closure handlers**: `on+Noun` or `perform` (e.g., `onTapGesture`, `perform action:`)
- **Zero-parameter getters**: Use computed property instead

### Mutating vs Non-mutating
- `mutating func sort()` → modifies original
- `func sorted() -> [Element]` → returns new copy

### Avoid
- ❌ `click` → ✅ `tap` or `select`
- ❌ `longClick` → ✅ `longPress`
- ❌ `get` prefix → ✅ Use noun form or computed property

### Examples
```swift
// ✅ Correct
func remove(at position: Index) -> Element
func cellForRow(at indexPath: IndexPath) -> UITableViewCell?
var lastItem: Element { get }

// ❌ Wrong
func getLastItem() -> Element
func click()
```

---

## 3. Closure Declarations

### Rules
- Define argument names for clarity
- Use typealias when argument names aren't sufficient

### Examples
```swift
// ✅ Correct
var fetchBooks: (_ bookName: String?, _ author: String?) async throws -> [Book]

typealias BookName = String
typealias AuthorName = String
var fetchBooks: (BookName?, AuthorName?) async throws -> [Book]

// ❌ Wrong
var handler: (String, Int) -> Void
```

---

## 4. Line Breaking and Formatting

### Rules
- **Line length**: 80-100 characters maximum
- **Function parameters**: Break at `(` and `,` when exceeding limit
- **Guard statements**: Break at commas for long conditions
- **File length**: Prefer 200-300 lines, consider splitting if much larger

### Examples
```swift
// ✅ Correct
func onTapGesture(
    count: Int = 1,
    perform action: @escaping () -> Void
) -> some View

guard message.channel == currentChannel,
      message.sender == me,
      message.isImportant else {
    return
}
```

---

## 5. Modern Swift Concurrency & APIs

### @Observable + @MainActor
```swift
// ✅ Correct
@Observable @MainActor
class AppSettings { }

// ❌ Wrong - Missing @MainActor
@Observable
class AppSettings { }
```

### Concurrency
- ✅ Use `async`/`await`, not GCD
- ✅ `Task.sleep(for: .seconds(1))`
- ❌ `DispatchQueue.main.async { }`
- ❌ `Task.sleep(nanoseconds: 1_000_000_000)`

### Force Unwrap
- Avoid `!` and `try!` except for unrecoverable cases
- Use `guard let`, optional chaining, or `??`

### String Search
- ✅ `localizedStandardContains()` for user input
- ❌ `contains()` for user-facing search

---

## 6. Modern Foundation APIs

### URL
```swift
// ✅ Modern
let documentsURL = URL.documentsDirectory
let fileURL = documentsURL.appending(path: "data.json")

// ❌ Legacy
let documentsURL = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
let fileURL = documentsURL.appendingPathComponent("data.json")
```

### String
```swift
// ✅ Swift native
let result = text.replacing("hello", with: "world")

// ❌ Foundation
let result = text.replacingOccurrences(of: "hello", with: "world")
```

### Number Formatting
```swift
// ✅ Modern format API
Text(myNumber, format: .number.precision(.fractionLength(2)))

// ❌ C-style
Text(String(format: "%.2f", myNumber))
```

### Static Member Lookup
```swift
// ✅ Preferred
Circle().fill(.blue)
Button("OK") { }.buttonStyle(.borderedProminent)

// ❌ Verbose
Circle().fill(Color.blue)
Button("OK") { }.buttonStyle(BorderedProminentButtonStyle())
```

---

## 7. Modern SwiftUI Modifiers

### Foreground
```swift
// ✅ Modern
.foregroundStyle(.blue)

// ❌ Deprecated
.foregroundColor(.blue)
```

### Clip Shape
```swift
// ✅ Modern
.clipShape(.rect(cornerRadius: 10))

// ❌ Old
.cornerRadius(10)
```

### Navigation
```swift
// ✅ Modern
NavigationStack {
    List(items) { item in
        NavigationLink(value: item) {
            ItemRow(item: item)
        }
    }
    .navigationDestination(for: Item.self) { item in
        DetailView(item: item)
    }
}

// ❌ Deprecated
NavigationView { }
```

### Other Modifiers
- ✅ `.scrollIndicators(.hidden)` ❌ `showsIndicators: false`
- ✅ `ImageRenderer` ❌ `UIGraphicsImageRenderer`
- ✅ `.bold()` ❌ `.fontWeight(.bold)` (unless other weights needed)
- ✅ `.font(.title)` ❌ `.font(.system(size: 24))`

---

## 8. Data Models

### Naming
```swift
// ✅ Preferred - No redundant suffix
@Observable class Library { }

// ✅ Acceptable - When needed
class EventData: ObservableObject { }

// Instance names
@StateObject private var library = Library()
@StateObject private var eventData = EventData()
```

### Observable
- iOS 17+: Use `@Observable` with `@MainActor`
- iOS 16-: Use `ObservableObject`
- Never mix both in same project targeting iOS 17+

---

## 9. View Naming

### Editor Views
- `*Editor` or `*EditView`
- Examples: `EventEditor`, `BookEditView`

### Lists
- `*List` for general lists
- `*Picker` for category selection
- Examples: `BookList`, `CategoryPicker`

### List Items
- `*Row` for list row items
- `*Column` for grid items
- `*ItemView` for other reusable items
- Examples: `BookRow`, `BookColumn`, `LibraryItemView`

---

## 10. SwiftUI Best Practices

### View Optimization
- Move complex logic to computed properties
- Avoid heavy calculations in `body`

### State Management
- `@State`: View owns the data
- `@Binding`: View receives reference from parent
- `@StateObject`: View owns ObservableObject (iOS 16-)
- `@ObservedObject`: View observes external object (iOS 16-)
- `@Environment`: View observes @Observable (iOS 17+)

### View Extraction
```swift
// ✅ Extract as View struct
struct HeaderView: View {
    var body: some View { }
}

// ❌ Computed property
private var headerView: some View { }
```

### onChange Modifier
```swift
// ✅ Correct
.onChange(of: value) { oldValue, newValue in }
.onChange(of: value) { }  // iOS 17+

// ❌ Deprecated
.onChange(of: value) { newValue in }
```

### Button vs onTapGesture
```swift
// ✅ Prefer Button
Button("Add", systemImage: "plus") { }

// ❌ Unnecessary onTapGesture
Image(systemName: "plus")
    .onTapGesture { }

// ✅ Valid onTapGesture usage (when location needed)
Canvas { }
    .onTapGesture { location in
        handleTap(at: location)
    }
```

### Button Accessibility
```swift
// ✅ Include text for accessibility
Button("Add Item", systemImage: "plus") { }

// ❌ Image only
Button { } label: {
    Image(systemName: "plus")
}
```

### GeometryReader Alternatives
```swift
// ✅ Use containerRelativeFrame or visualEffect
Rectangle()
    .containerRelativeFrame(.horizontal) { length, _ in
        length * 0.5
    }

// ❌ Unnecessary GeometryReader
GeometryReader { geometry in
    Rectangle()
        .frame(width: geometry.size.width * 0.5)
}
```

### AnyView
```swift
// ✅ Use @ViewBuilder
@ViewBuilder
var content: some View {
    if condition {
        ViewA()
    } else {
        ViewB()
    }
}

// ❌ AnyView (performance impact)
var content: some View {
    if condition {
        AnyView(ViewA())
    } else {
        AnyView(ViewB())
    }
}
```

### Other Guidelines
- Avoid hardcoded padding/spacing unless requested
- Avoid UIKit colors in SwiftUI (use `Color`)
- Don't convert `enumerated()` to array in `ForEach`
- Never use `UIScreen.main.bounds` for size
- Use PreferenceKey/Environment for cross-view communication

---

## 11. Project Structure

### File Rules
- **One type per file**: Each struct/class/enum in separate file
- **File names match types**: `LoginView.swift` contains `LoginView`
- **Consistent suffixes**:
  - View: `*View`, `*Screen`
  - ViewModel: `*ViewModel`
  - Model: no suffix or `*Data`
  - Service: `*Service`, `*Manager`

### SwiftData + CloudKit Constraints
When using CloudKit sync:
- ❌ **Never use `@Attribute(.unique)`** → Causes sync failures
- ✅ **All properties**: Must have default values OR be optional
- ✅ **All relationships**: Must be optional

```swift
// ❌ Wrong
@Model
class Book {
    @Attribute(.unique) var isbn: String
    var title: String
    var author: Author
}

// ✅ Correct
@Model
class Book {
    var isbn: String
    var title: String = ""
    var author: Author?
}
```

### Testing
- **Unit tests**: Core business logic
- **UI tests**: Only when unit tests not possible

### Documentation
- Add comments for complex logic
- Documentation comments for public APIs
- Comments explain "why", not "what"

### Security
- **No secrets in code**: API keys from environment variables
- **Add to .gitignore**: `.env`, `credentials.json`, `secrets.plist`

```swift
// ❌ Hardcoded
let apiKey = "sk_live_abc123xyz"

// ✅ From environment
let apiKey = ProcessInfo.processInfo.environment["API_KEY"] ?? ""
```

### Third-party Frameworks
Before adding dependencies, verify:
1. Can't be solved with Swift standard library
2. Can't be solved with Apple frameworks
3. Library is actively maintained
4. License compatible with project

### UIKit Usage
Avoid UIKit unless:
- Feature not available in SwiftUI
- Integrating with existing UIKit codebase
- Explicitly requested by user

---

## Vocabulary

### Controls
Views that enable user interaction (buttons, links, pickers)

### Indicators
Views that display information (progress views, gauges)

### Field vs Editor
- **Field**: Control-level editing unit
- **Editor**: View-level editing unit

### Data Model vs Model Data
- **Data Model**: Observable class (`@Observable` or `ObservableObject`)
- **Model Data**: Instance of a data model

---

## References

- Korean detailed guide: `convention-KR.md`
- Apple HIG: `HIG/APPLE-HIG-EN.md`
- [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
- [Managing Model Data - Apple](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app)
