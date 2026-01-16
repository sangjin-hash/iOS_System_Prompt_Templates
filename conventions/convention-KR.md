# Swift Code Style Guide

이 문서는 Swift 및 SwiftUI 개발 시 일관성 있는 코드 스타일을 유지하기 위한 컨벤션 가이드입니다.

---

## 📚 목차

### Fundamental
1. [Boolean](#1-boolean)
2. [Closure](#2-closure)
3. [Function](#3-function)
4. [Line Breaking and Limit](#4-line-breaking-and-limit)

### Modern Swift
5. [Swift Concurrency & Modern API](#5-swift-concurrency--modern-api)
6. [Foundation & String API](#6-foundation--string-api)

### SwiftUI
7. [SwiftUI Best Practices](#7-swiftui-best-practices)
8. [Data Model](#8-data-model)
9. [View](#9-view)
10. [Vocabulary](#10-vocabulary)

### Code Organization
11. [Project Structure](#11-project-structure)

---

# Fundamental

## 1. Boolean

### 3가지 명명 방식

1. **is+과거분사** (예: `var isEnabled: Bool`)
2. **3인칭단수** (예: `var showsCancelButton: Bool`)
3. **과거분사** (예: `func present(animated: Bool)`)

### 사용 규칙

1. `@State`/`@Binding` → **is+과거분사**
2. 파라미터 → **과거분사**
3. 설정/옵션 → **3인칭단수**
4. SwiftUI 일반 변수 → **3인칭단수**
5. 나머지 → **is+과거분사**

### 예시

**파라미터:** 과거분사 형태 (argument 이름에 `is` 붙이지 않음)
```swift
func present(_ viewController: UIViewController, animated: Bool = true)
```

**설정값:** 3인칭 단수
```swift
struct AppSettings {
    var showsProfileImage: Bool = false
    var enablesDarkTheme: Bool = false
}
```

**@State/@Binding:** is+과거분사
```swift
@State private var isAddingNewEvent = false
@Binding var isAddingNewEvent: Bool

.sheet(isPresented: $isAddingNewEvent) {
    EventEditor(event: $newEvent)
}
```

**일반:** is+과거분사
```swift
struct Event {
    var isCompleted: Bool = false
    var isOutdated: Bool { endDate < Date() }
}
```

---

## 2. Closure

### 선언부

Argument 이름을 정의하면 클로저 사용 시 자동으로 적용됨.

```swift
struct BookStore {
    var fetchBooks: (_ bookName: String?, _ author: String?) async throws -> [Book]
}

// 사용 시 자동으로 argument 이름 사용
BookStore { bookName, author in }
```

**왜?** 파라미터 역할 파악 용이.

Argument 이름이 불충분한 경우 typealias 사용.

```swift
typealias BookName = String
typealias AuthorName = String

var fetchBooks: (BookName?, AuthorName?) async throws -> [Book]

// 객체 생성 시 타입이 명확하게 표시됨
BookStore(
    fetchBooks: (BookName?, AuthorName?) async throws -> [Book]
)
```

**왜?** `(String?, String?)`만 보면 역할을 알 수 없음. Typealias로 명확성 확보.

---

## 3. Function

### 명명 규칙

- **동사형:** 일반적인 경우 (예: `remove(at:)`)
- **명사형:** 값 리턴이 목적 (예: `cellForRow(at:)`)
- **on+명사:** 클로저 실행 목적 (예: `onTapGesture(perform:)`)

### 상세규칙

**Computed property 고려:** 값 리턴 + 파라미터 없음
```swift
// ❌ func getLastItem() -> Element
// ✅ var lastItem: Element { get }
```

**Mutating vs Non-mutating:**
```swift
books.sort()           // 원본 수정
let sorted = books.sorted()  // 새 객체 리턴
```

**문장 형태 파라미터:**
```swift
foods.remove(at: 0)
let cell = tableView.cellForRow(at: indexPath)
```

**Closure 파라미터:** `perform(action:)` 또는 `on+명사`
```swift
onTapGesture(perform: { })
doSomeNetworkRequest(onRequest:onCompletion:)
```

**지양 단어:**
- ❌ `click` → ✅ `tap`, `select`
- ❌ `longClick` → ✅ `longPress`
- ❌ `get` → ✅ 명사형 또는 computed property

---

## 4. Line Breaking and Limit

### 규칙

1. **한 줄 80-100자 제한**
2. **한 파일 200-300줄 권장** (300줄 이상이면 분리 고려)
3. **가독성 우선: 과감하게 줄바꿈**
4. **파라미터 많으면: `(`, `,` 기준으로 줄 구분**

```swift
func onTapGesture(
    count: Int = 1,
    perform action: @escaping () -> Void
) -> some View
```

5. **조건문 많으면: 쉼표 기준 줄 구분 또는 여러 조건문으로 분리**

```swift
// 쉼표 기준 줄 구분
guard message.channel == currentChannel,
    message.sender == me,
    message.isImportant else { return }

// 여러 조건문으로 분리
guard message.channel == currentChannel, message.sender == me else { return }
guard message.isImportant else { return }
```

---

# Modern Swift

## 5. Swift Concurrency & Modern API

iOS 17+ 및 Swift 6.2+에서는 modern concurrency와 최신 API를 사용한다.

### @Observable과 @MainActor

UI 관련 `@Observable` 클래스는 `@MainActor` 필수.

```swift
// ✅ 올바른 사용
@Observable @MainActor
class AppSettings {
    var theme: Theme = .light
}

// ❌ @MainActor 누락
@Observable
class AppSettings { }
```

**왜?** Strict concurrency에서 UI는 main thread 전용. 컴파일 타임 안전성 보장.

**iOS 17+:** `ObservableObject` 대신 `@Observable` 사용.

```swift
// ✅ @Observable + @Environment
@Observable @MainActor
class Library { var books: [Book] = [] }

@Environment(Library.self) private var library

// ❌ ObservableObject (iOS 16 방식)
class Library: ObservableObject { @Published var books: [Book] = [] }
```

### Modern Concurrency

GCD 대신 async/await 사용.

```swift
// ✅ Modern concurrency
Task {
    let data = await fetchData()
}

// ❌ GCD (구식)
DispatchQueue.main.async { }
```

**Task.sleep:** `for: .seconds(1)` 사용 (nanoseconds 금지).

### String 검색

사용자 입력 필터링: `localizedStandardContains()` 사용.

```swift
// ✅ 로케일 고려
let filtered = items.filter { $0.name.localizedStandardContains(searchText) }

// ❌ 로케일 무시
let filtered = items.filter { $0.name.contains(searchText) }
```

**왜?** 대소문자, 악센트, 로케일 자동 처리.

### Force Unwrap 지양

`!`와 `try!`는 복구 불가능한 상황에서만.

```swift
// ✅ 안전한 처리
guard let user = fetchUser() else { return }
let name = user.name ?? "Unknown"

// ❌ 크래시 위험
let user = fetchUser()!

// ✅ 예외 (필수 리소스)
let image = UIImage(named: "logo")! // 앱 실행 필수
```

---

## 6. Foundation & String API

### Foundation Modern API

최신 Foundation API를 우선적으로 사용한다.

#### URL API

```swift
// ✅ Modern API
let documentsURL = URL.documentsDirectory
let fileURL = documentsURL.appending(path: "data.json")
```

```swift
// ❌ Legacy API
let documentsURL = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)[0]
let fileURL = documentsURL.appendingPathComponent("data.json")
```

### String API

Swift native string 메서드를 Foundation 메서드보다 우선한다.

#### replacing vs replacingOccurrences

```swift
// ✅ Swift native
let result = text.replacing("hello", with: "world")
```

```swift
// ❌ Foundation 방식
let result = text.replacingOccurrences(of: "hello", with: "world")
```

### Number Formatting

C-style formatting 대신 modern format API를 사용한다.

```swift
// ✅ Modern format API
Text(abs(myNumber), format: .number.precision(.fractionLength(2)))
```

```swift
// ❌ C-style formatting
Text(String(format: "%.2f", abs(myNumber)))
```

**왜?**

Modern format API는 타입 안전성과 로케일 자동 처리를 제공한다.

### Static Member Lookup

가능한 경우 static member lookup을 사용하여 코드를 간결하게 유지한다.

```swift
// ✅ Static member lookup
Circle()
    .fill(.blue)
    .frame(width: 100, height: 100)

Button("Submit") { }
    .buttonStyle(.borderedProminent)
```

```swift
// ❌ 전체 타입 명시
Circle()
    .fill(Color.blue)
    .frame(width: 100, height: 100)

Button("Submit") { }
    .buttonStyle(BorderedProminentButtonStyle())
```

---

# SwiftUI

## 7. SwiftUI Best Practices

### Modern SwiftUI Modifiers

최신 SwiftUI modifier를 사용하고, deprecated API를 지양한다.

```swift
// ✅ Modern API
.foregroundStyle(.blue)          // ❌ .foregroundColor(.blue)
.clipShape(.rect(cornerRadius: 10))  // ❌ .cornerRadius(10)
.scrollIndicators(.hidden)       // ❌ ScrollView(showsIndicators: false)
.bold()                          // ❌ .fontWeight(.bold) (bold만 필요한 경우)
```

**Tab API**
```swift
// ✅ Modern
TabView {
    Tab("Home", systemImage: "house") { HomeView() }
}

// ❌ Old
TabView {
    HomeView().tabItem { Label("Home", systemImage: "house") }
}
```

**NavigationStack**
```swift
// ✅ Modern
NavigationStack {
    List(items) { item in
        NavigationLink(value: item) { ItemRow(item: item) }
    }
    .navigationDestination(for: Item.self) { ItemDetailView(item: $0) }
}

// ❌ NavigationView (deprecated)
```

### onChange Modifier

1-parameter variant 사용 금지.

```swift
// ✅ 2-parameter 또는 0-parameter (iOS 17+)
.onChange(of: searchText) { oldValue, newValue in }
.onChange(of: searchText) { }

// ❌ 1-parameter (deprecated)
```

### Button vs onTapGesture

특정 이유 없이 `Button` 사용.

```swift
// ✅ Button 우선
Button("Add Item", systemImage: "plus") { addItem() }

// ✅ onTapGesture 정당한 경우 (탭 위치 필요)
Canvas { }.onTapGesture { location in handleTap(at: location) }

// ❌ 불필요한 onTapGesture
Image(systemName: "plus").onTapGesture { addItem() }
```

**접근성:** 버튼에 이미지만 사용하지 말고 텍스트 함께 명시.

### Font & Layout

```swift
// ✅ Dynamic Type
Text("Title").font(.title)

// ❌ 하드코딩
Text("Title").font(.system(size: 24))
```

**GeometryReader 대안:** `containerRelativeFrame` 또는 `visualEffect` 사용.

```swift
// ✅ containerRelativeFrame
Rectangle().containerRelativeFrame(.horizontal) { length, _ in length * 0.5 }

// ❌ 불필요한 GeometryReader
```

### View Extraction

Computed property가 아닌 View struct로 분리.

```swift
// ✅ View struct
struct HeaderView: View { }

// ❌ Computed property
private var headerView: some View { }
```

**왜?** 재사용성과 테스트 가능성 향상.

### View Model

뷰 로직은 view model로 분리.

```swift
// ✅ View model 분리
@Observable @MainActor
class BookListViewModel {
    var books: [Book] = []
    func loadBooks() async { }
}
```

### AnyView 지양

`@ViewBuilder` 사용. `AnyView`는 성능 저하 가능.

```swift
// ✅ @ViewBuilder
@ViewBuilder
var content: some View {
    if isLoggedIn { HomeView() } else { LoginView() }
}

// ❌ AnyView
```

### 기타 지양 사항

- ❌ 하드코딩된 padding/spacing (요청 없으면)
- ❌ UIKit 색상 (`Color(UIColor.systemBlue)`)
- ❌ `Array(items.enumerated())` (직접 `enumerated()` 사용)
- ❌ `UIScreen.main.bounds` (containerRelativeFrame 사용)
- ❌ `UIGraphicsImageRenderer` (SwiftUI `ImageRenderer` 사용)

---

## 8. Data Model

### 기본 원칙

[Model Data - Apple Developer](https://developer.apple.com/documentation/swiftui/model-data)

1. **가급적 Model, Data 가 없이 사용할 수 있는 단어를 사용할 것**

```swift
@Observable class Library
```

[Managing model data in your app - Apple, Inc](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app#:~:text=%40Observable%20class%20Library%20%7B)

2. **그 외의 경우 모델명 + `Data` 로 끝낼 것 사용**

```swift
// Swift Playground - Date Planner by Apple, Inc.
class EventData: ObservableObject
```

```swift
// Updating an app to use Swift concurrency - Apple, Inc.
class CoffeeData: ObservableObject
```

### 모델 데이터 이름

데이터 모델명을 그대로 사용.

```swift
@StateObject private var library = Library()
```

```swift
@StateObject private var coffeeData = CoffeeData()
```

다른 데이터 모델이 없는 경우, 어떤 역할을 하는 모델 데이터인지 명확하게 알 수 있는 상황일 때 modelData 명칭을 사용해도 됨.

```swift
@StateObject private var eventData = EventData()
@State private var event = Event()
```

---

## 9. View

### 뷰 네이밍

#### 1. 편집 기능이 메인인가?

- **`*Editor`**
  > **참고**: [Updating an app to use swift concurrency](https://developer.apple.com/documentation/swift/updating_an_app_to_use_swift_concurrency)

```swift
/// Swift Playground - Date Planner by Apple, Inc.
struct EventEditor: View { }
```

- **`*EditView`**
  > **참고**: [Managing model data in your app - Apple, Inc.](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app#:~:text=struct%20BookEditView%3A%20View%20%7B)

```swift
struct BookEditView: View { }
```

#### 2. 목록에 대한 뷰인가?

1. 카테고리 선택에 사용하는가?
   → **`*Picker`**

2. 정보 나열인가? (일반적인 케이스)
   → **`*List`**

#### 3. 나열되는 아이템에 대한 뷰인가?

1. 아이템이 `List` 의 row content 로 사용되는가?
   → **`*Row`**

2. 아이템이 `Grid`의 아이템으로 사용되는가?
   → **`*Column`**

3. 기타
   → **`*ItemView`**
   > **참고**: [Managing model data in your app - Apple, Inc.](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app#:~:text=of%20%60book%60%20changes.-,struct%20LibraryItemView%3A%20View%20%7B,-var%20book%3A)

```swift
struct LibraryItemView: View {
```

---

## 10. Vocabulary

### 용어 정리

#### 뷰

##### Controls

플랫폼과 컨텍스트마다 특화된 유저 인터랙션을 허용해주는 뷰. 예를 들어, 버튼, 링크, 피커가 있다.
[Controls and indicators - Apple, Inc.](https://developer.apple.com/documentation/swiftui/controls-and-indicators?language=_5#:~:text=enable%20user%20interaction%20specific%20to%20each%20platform%20and%20context)

##### Indicators

사용자에게 정보를 보여주는 뷰. 예를 들어, Progress View 와 게이지 형태가 있다.

##### Field & Editor

- **Field**: 편집에 대한 컨트롤 단위.
- **Editor**: 편집에 대한 뷰 단위.

#### 데이터 모델

**데이터 모델과 모델 데이터 (Data Model & Model Data)**

[Managing model data in your app - Apple, Inc.](https://developer.apple.com/documentation/swiftui/managing-model-data-in-your-app#:~:text=that%20is%2C%20an%20instance%20of%20a%20data%20model)

```swift
// Swift Playground - Date Planner by Apple, Inc.
class EventData: ObservableObject
```

1. `ObservableObject` 를 준수하여 관찰 가능한 클래스를 **데이터 모델**이라고 한다.
   - iOS 17, iPadOS17, macOS 14, tvOS 17, watchOS 10 부터는 `@Observable()` 매크로가 적용된 클래스를 데이터 모델이라고 한다.

2. 데이터 모델의 인스턴스를 **모델 데이터**라고 한다.

---

# Code Organization

## 11. Project Structure

### 파일 분리 규칙

하나의 파일에 하나의 타입.

```swift
// ✅ User.swift
struct User { }

// ✅ UserViewModel.swift
@Observable @MainActor
class UserViewModel { }

// ❌ UserModule.swift (여러 타입 혼재)
```

**예외:** 밀접하게 관련된 작은 타입 (예: `NetworkError`와 `NetworkResponse`)

**파일명:** 타입명과 동일.

**접미사:** View (`*View`), ViewModel (`*ViewModel`), Model (없음 또는 `*Data`), Service (`*Service`, `*Manager`), Extension (`String+Extensions.swift`)

### SwiftData + CloudKit 규칙

```swift
// ❌ CloudKit 불가
@Model
class Book {
    @Attribute(.unique) var isbn: String  // unique 금지
    var title: String                     // 기본값 없음
    var author: Author                    // relationship non-optional
}

// ✅ CloudKit 호환
@Model
class Book {
    var isbn: String
    var title: String = ""  // 기본값 또는 optional
    var author: Author?     // relationship optional
}
```

**핵심:**
- ❌ `@Attribute(.unique)` 사용 금지
- ✅ 모든 프로퍼티: 기본값 또는 optional
- ✅ 모든 relationship: optional

### 테스트

**Unit Test 우선:** 핵심 비즈니스 로직 테스트.

```swift
@Test("북 목록 로딩 테스트")
func testLoadBooks() async throws {
    let viewModel = BookViewModel(bookService: MockBookService())
    await viewModel.loadBooks()
    #expect(viewModel.books.count > 0)
}
```

**UI Test 최소화:** Unit test로 불가능한 경우만.

### 문서화

**복잡한 로직:** 주석 추가 (왜?).

```swift
/// 사용자 구독 만료 여부 반환
/// - 무료 사용자: false
/// - 유료 사용자: 구독 종료일 vs 현재 시간
func isSubscriptionExpired() -> Bool {
    guard let subscription = user.subscription else { return false }
    return subscription.endDate < Date()
}
```

**공개 API:** Documentation comments.

```swift
/// 네트워크 요청 서비스
/// - Note: 의존성 주입 사용
class NetworkService {
    /// URL에서 데이터 가져오기
    /// - Parameter url: 데이터 URL
    /// - Returns: Data
    /// - Throws: NetworkError
    func fetch(from url: URL) async throws -> Data { }
}
```

### 보안

**Secrets 금지:** 코드에 API key 하드코딩 금지.

```swift
// ❌ 하드코딩
let apiKey = "sk_live_abc123xyz"

// ✅ 환경 변수
let apiKey = ProcessInfo.processInfo.environment["API_KEY"] ?? ""
```

**.gitignore 추가:** `.env`, `credentials.json`, `secrets.plist`, `Config/Secrets.swift`

### Third-party

도입 전 확인:
1. Swift 표준 라이브러리로 해결 가능?
2. Apple 공식 프레임워크로 해결 가능?
3. 유지보수 활발?
4. 라이선스 호환?

### UIKit

SwiftUI 우선. UIKit 사용 조건:
- SwiftUI 미지원 기능
- 기존 UIKit 코드베이스 통합
- 명시적 요청