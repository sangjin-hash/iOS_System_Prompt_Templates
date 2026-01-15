# Swift Code Style Guide

이 문서는 Swift 및 SwiftUI 개발 시 일관성 있는 코드 스타일을 유지하기 위한 컨벤션 가이드입니다.

---

## 📚 목차

### Fundamental
1. [Boolean](#1-boolean)
2. [Closure](#2-closure)
3. [Function](#3-function)
4. [Line Breaking and Limit](#4-line-breaking-and-limit)

### SwiftUI
5. [Data Model](#5-data-model)
6. [View](#6-view)
7. [Vocabulary](#7-vocabulary)

---

# Fundamental

## 1. Boolean

### 기본

Bool 타입의 변수의 명명 방식은 다음과 같이 3가지가 있다.

1. **is+과거분사 형태**
```swift
var isEnabled: Bool
```

2. **3인칭단수형태**
```swift
var showsCancelButton: Bool
```

3. **과거분사 형태**
```swift
func viewWillAppear(animated: Bool)
```

### 다이어그램

다음 순서에 따라 Bool 변수명을 짓도록 한다.

1. `@State` 이나 `@Binding` 을 사용하는가? → **1번**
2. 파라미터에 사용하는가? → **3번**
3. 설정값에 옵션을 켜는 용도인가? → **2번**
4. `SwiftUI.View` 에서 사용하는데 `@State` 이나 `@Binding` 을 사용하는 것이 아닌가? → **2번**
5. 나머지 → **1번**

### 상세설명

다음과 같이 사용 규칙을 상세하게 정리한다.

#### 과거분사 형태

파라미터명에 사용.
단, argument 이름에는 `is+과거분사` 형태를 사용하거나 명명하지 않는다.

```swift
func doSomethingA(selected: Bool)

func doSomethingB(animated: Bool)

func present(_ viewController: UIViewController, animated: Bool = true)
```

#### 3인칭 단수 형태

무엇의 상태가 아닌, 어떤 옵션을 켜는 등의 상태를 바꾸려는 역할을 수행하는 경우 3인칭 단수 형태를 사용

```swift
struct AppSettings {
    var showsProfileImage: Bool = false
    var enablesDarkTheme: Bool = false
    var animatesWhenLoading: Bool = false
}

let settings = AppSettings()
settings.showsProfileImage = true
settings.enablesDarkTheme = true
```

#### is+과거분사 형태

위의 2가지 방식 외에 나머지 모든 경우에서는 이 형태를 사용한다.
특히 SwiftUI 에서는 State 또는 Binding 키워드를 사용하는 변수에 is+과거분사 형태를 사용하고, 일반 변수는 3인칭 단수 형태를 쓰는 방식으로 구분한다.

```swift
struct Event {
    var endDate: Date = .now
    var isCompleted: Bool = false
    var isOutdated: Bool {
        Calendar.current.dateComponents([.month, .day], from: endDate, to: .now).day! >= 0
    }
}
```

```swift
@State private var isAddingNewEvent = false
@Binding var isAddingNewEvent: Bool
```

```swift
.sheet(isPresented: $isAddingNewEvent) {
    EventEditor(event: $newEvent)
}
```

---

## 2. Closure

### 선언부

#### argument 이름을 정하면 클로져 사용시에 해당이름을 자동으로 사용

```swift
struct BookStore {
    var fetchBooks: (_ bookName: String?, _ author: String?) async throws -> [Book]
}
```

**왜?**

사용할 때 자동으로 argument 이름을 사용하기 때문에 어떤 파라미터를 다루는 핸들러인지 파악이 쉬움

```swift
BookStore { bookName, author in
    // ...
}
```

#### argument 이름이 아닌 타입으로 확인해야하는 경우, typealias 를 통해 타입 이름을 활용할 것

```swift
typealias BookName = String
typealias AuthorName = String

struct BookStore {
    var fetchBooks: (BookName?, AuthorName?) async throws -> [Book]
}
```

**왜?**

```swift
BookStore(
    fetchBooks: (String?, String?) async throws -> [Book]
)
```

`BookStore` 객체 생성시에 생성자에 클로져는 arg의 타입만 보여주게 된다. 엔터를 누르면 앞서 말한 것 처럼 arguement에 정의했던 이름이 자동으로 적용되지만

```swift
BookStore { bookName, author in
    // ...
}
```

`fetchBooks` 이라는 파라미터명을 살리고 싶은 경우 이 상태에서 바로 사용해야한다.

```swift
BookStore(
    fetchBooks: {  }
)
```

하지만 아래와 같은 내용만 봐서는 각 argument 가 무슨 역할인지 알 수 없다.

```swift
(String?, String?) async throws -> [Book]
```

따라서 `typealias` 를 적극적으로 사용하도록 한다.

```swift
typealias BookName = String
typealias AuthorName = String

var fetchBooks: (BookName?, AuthorName?) async throws -> [Book]
```

그러면 `BookStore` 객체 생성시 다음과 같이 클로져의 타입이 표시된다.

```swift
BookStore(
    fetchBooks: (BookName?, AuthorName?) async throws -> [Book]
)
```

---

## 3. Function

### 기본

기본적으로 함수를 명명할 때는 동사 원형으로 합니다. 하지만 값을 리턴하는 것이 목적인 경우 명사형으로 명명합니다.

```swift
// 1. 동사형 - 일반적인 경우
func remove(at position: Index) -> Element // 값을 리턴하지만 리턴이 주 목적이 아니라, 제거하는 것이 주 목적

// 2. 명사형 - 값 리턴이 목적인 경우
func cellForRow(at indexPath: IndexPath) -> UITableViewCell?
```

함수를 가진 객체가 대상이고, closure 를 실행시키는 것이 목적인 함수의 경우 함수 역할을 더 잘 전달하기 위해, on+명사 형태를 쓸 수 있습니다.

```swift
/// 자신을 호출한 뷰에 대해서, action 을 perform 하는 것이 메인 목적인 함수
func onTapGesture(
    count: Int = 1,
    perform action: @escaping () -> Void
) -> some View
```

### 상세규칙

다음과 같이 상세 규칙을 정리합니다.

#### 함수에 어울리지 않는 경우

만약 다음 두 조건을 만족한다면, 함수가 아닌 computed property 를 사용하는 것을 고려해볼 것

1. 함수가 값을 리턴하는가?
2. 파라미터가 없는가?

```swift
- func getLastItem() -> Element
+ var lastItem: Element { get }
```

get, set 이름으로 시작할까 고민인데 파라미터가 필요없는 경우라면 프로퍼티에 get, set 을 정의해볼 수 있지 않은지 고민해볼 것

#### 기존 객체에 영향을 주는지 안주는지에 따라 네이밍

```swift
var books: [Book] = [.어린왕자, .로빈슨크루소, .톰소여의모험]

books.sort() // books 를 정렬
let sortedBooks = books.sorted() // books 는 그대로, 정렬된 새로운 객체 리턴
```

#### 함수 사용시 하나의 문장이 될 수 있도록 파라미터명을 지을 것

```swift
foods.remove(at: 0)
let cell = tableView.cellForRow(at: indexPath)
```

#### closure 를 받는 경우 perform(argument 이름은 action) 또는 on+명사 형태를 파라미터로 쓸 것

```swift
onTapGesture(perform: {
  // action
})
```

```swift
doSomeNetworkRequest(onRequest:onCompletion:)
```

#### 사용을 지양하는 단어

| 지양 | 권장 | 비고 |
| --- | --- | --- |
| click | tap, select | 일반적으로는 tap(예: `onTapGesture`), 만약 여러개 중에 하나를 선택하는 액션이라면 select (예: `selectItem(at:animated:scrollPosition:)`) |
| longClick, longTap | longPress | 예: `onLongPressGesture` |
| get | 명사형 이름 | 예: `cellForRow(at:)` |

---

## 4. Line Breaking and Limit

### 기본

1. **가급적 한 줄 당 80 (또는 100자) 를 넘기지 않도록 한다.**

2. **한 파일에 코드 수는 2-300자가 가장 보기 좋다.**(300자를 넘기지 말라는 의미는 아님)
   다만 300자가 넘어가는 경우 기능에 따라, 중요도 따라, 접근성에 따라 파일 분리를 고민하는 것이 좋다.

3. **가독성을 위해 코드 줄 수 보다 한 줄에 작성된 코드 길이에 더 초점을 맞추고 과감하게 엔터를 누를 것**

4. **생성자나 함수의 파라미터가 많아서 한줄 허용치를 넘길 경우, 괄호(`(`) 와 쉼표(`,`)를 기준으로 줄 구분을 한다.**

```swift
func onTapGesture(     // 엔터
    count: Int = 1,     // 엔터
    perform action: @escaping () -> Void // 엔터
) -> some View
```

```swift
let cell = tableView.dequeueCellReusableCell(withIdentifier: .customCell, for: indexPath) as! MyCustomTableViewCell

let cell = tableView.dequeueCellReusableCell(
    withIdentifier: .customCell,
    for: indexPath
) as! MyCustomTableViewCell
```

5. **조건문이 많아서 한줄 허용치를 넘기는 경우, 쉼표를 기준으로 줄 구분을 하거나 여러개의 조건문으로 적절히 분리한다**

```swift
// 조건문이 너무 많은 경우
guard message.channel == currentChannel, message.sender == me, message.isImportant, currentChannel.allowsImportantMessage else {
    return
}
showMessageAsImportant()
```

```swift
// 쉼표를 기준으로 줄 구분하기
guard message.channel == currentChannel,
    message.sender == me,
    message.isImportant,
    currentChannel.allowsImportantMessage else {
    return
}
showMessageAsImportant()
```

```swift
// 여러개의 조건문으로 적절히 분리하기
guard message.channel == currentChannel, message.sender == me else { return }
guard message.isImportant, currentChannel.allowsImportantMessage else { return }

showMessageAsImportant()
```

---

# SwiftUI

## 5. Data Model

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

## 6. View

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

## 7. Vocabulary

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