---
description: Learn the 'result builder' introduced in Swift 5.4
---

# \[Swift] Result Builder

## ResultBuilder

* _A result builder_ is a type you define that adds syntax for creating nested data in a natural, declarative way.
* A _result builder_ is a type that builds a nested data structure step by step.
* You can apply `@resultBuilder` attribute to a class, structure, enum to use that type as a result builder.
* You can apply result builders to function's parameter or computed properties.
* e.g. SwiftUI's `@ViewBuilder`

```swift
@resultBuilder
struct DrawingBuilder {
    static func buildBlock(_ components: Drawable...) -> Drawable {
        return Line(elements: components)
    }
    static func buildEither(first: Drawable) -> Drawable {
        return first
    }
    static func buildEither(second: Drawable) -> Drawable {
        return second
    }
}

// Use at function's parameter
func draw(@DrawingBuilder content: () -> Drawable) -> Drawable {
    return content()
}

// Use at computed property
@DrawingBuilder var lines: [Line] {
    ...
}
```

> [`@DrawingBuilder` Sample](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/advancedoperators/#Result-Builders)

## Methods

### Block-building methods

#### Build block

```swift
static func buildBlock(_ components: Component...) -> Component
```

* Combines an array of partial results into a single partial result.
* You must implement this method. (Required)

#### Build partial block

```swift
static func buildPartialBlock(first: Component) -> Component
static func buildPartialBlock(accumulated: Component, next: Component) -> Component
```

* Introduced in Swift 5.7
* Builds a partial result component by combining an first component or accumulated component with a new component.
* Implement both these methods to support building blocks one component at a time.
* Compared to `buildBlock(_:)`, this approach **reduces the need for generic overloads that handle different numbers of arguments.**
* Also see:
  * [https://github.com/apple/swift-evolution/blob/main/proposals/0348-buildpartialblock.md](https://github.com/apple/swift-evolution/blob/main/proposals/0348-buildpartialblock.md)
  * [https://www.hackingwithswift.com/swift/5.7/buildpartialblock](https://www.hackingwithswift.com/swift/5.7/buildpartialblock)

### Result-building methods

#### Build optional

```swift
static func buildOptional(_ component: Component?) -> Component
```

* Builds a partial result from a partial result that **can be  `nil`.**
* Implement this method to support `if` statements that **don’t include an `else` clause.**

#### Build depending on some condition

```swift
static func buildEither(first: Component) -> Component
static func buildEither(second: Component) -> Component
```

* Builds a partial result whose value varies depending on some condition.
* Implement both these methods to support **`switch` and `if` statements that include an `else` clause.**

#### Build array

```swift
static func buildArray(_ components: [Component]) -> Component
```

* Builds a partial result from an array of partial results.
* Implement this method to support **`for` loops.**

#### Perform Preprocessing before each build

```swift
static func buildExpression(_ expression: Expression) -> Component
```

* Builds a partial result from an expression.
* You can implement this method to perform preprocessing(e.g. converting expressions to an internal type) or to provide additional information for type inference at use sites.

#### Build result value

```swift
static func buildFinalResult(_ component: Component) -> Component
```

* Builds a final result from a partial result.

#### Build limited availabilty

```swift
static func buildLimitedAvailability(_ component: Component) -> Component
```

*

## Method call priority

1. Block(`{ }`)에 포함된 표현식
   1. 모든 표현식에 대해 `buildExpression(_:)` 호출
   2. 모든 `buildExpression(_:)` 반환값들을 array로 모아서 `buildBlock(_:)` 호출
2. For loop
   1. Loop 수 만큼 2번 반복
   2. Loop 수 만큼 `buildBlock(_:)`의 반환값들을 array로 모아서 `buildArray(_:)` 호출
3. Condition (if-else, if let)
   1. 실행될 statement block에 대해 2번 실행
   2. Else가 없을 때
      1. `buildOptional(_:)` 호출
      2. Condition이 `false`라면 `nil` 전달
   3. Else가 있을 때
      1. Condition이 `true`라면 `buildEither(first:)` 호출
      2. Conditioni이 `false`라면 `buildEither(second:)` 호출
4. Availability (`#available`)
   1. 실행될 statement block에 대해 2번 실행
   2. Condition이 `true`라면 `buildLimitedAvailability(_:)` 호출
   3. Else가 없을 때
      1. `buildOptional(_:)` 호출
      2. Condition이 `true`라면 `buildLimitedAvailability(_:)` 반환값 전달
      3. Condition이 `false`라면 `nil` 전달
   4. Else가 있을 때
      1. Condition이 `true`라면 `buildEither(first:)` 호출
      2. Condition이 `false`라면 `buildEither(second:)` 호출
5. 모든 표현식에 대해 처리가 끝난 뒤 `buildFinalResult(_:)` 호출
   1. 각 case에서 최종 변환된 값들이 array로 전달된다.

### Sample Code

<details>

<summary>HTML string을 만들어 주는 result builder 구현</summary>

다른 method들이 어떻게 동작하는지 확인하기 위해 임의의 문자열들을 더해본다.

#### HTMLBuilder 구현

```swift
@resultBuilder
struct HTMLBuilder {
    static func buildBlock(_ components: String...) -> String {
        return components.joined(separator: "\n")
    }
    static func buildExpression(_ expression: String) -> String {
        return "a" + expression
    }
    static func buildOptional(_ component: String?) -> String {
        return component ?? "none"
    }
    
    static func buildEither(first component: String) -> String {
        return "first: " + component
    }
    
    static func buildEither(second component: String) -> String {
        return "second: " + component
    }
    
    static func buildFinalResult(_ component: String) -> String {
        return component + "END"
    }
    
    static func buildLimitedAvailability(_ component: String) -> String {
        return component
    }
    
    static func buildArray(_ components: [String]) -> String {
        return components.reduce("", +)
    }
}

```

#### DSL로 HTML string 만들기

```swift

func createHTML(@HTMLBuilder _ content: () -> String) -> String {
    return content()
}

func header(_ exists: Bool) -> String? {
    exists ? "<h1>Hello, World!</h1>" : nil
}


@available (iOS 16.0, *)
struct Maker {
    static func make() -> String {
        return "iOS 18.0"
    }
}

let html = createHTML {
    "<html>"
    "<head>"
    "<title>Sample Page</title>"
    "</head>"
    "<body>"
    
    for i in 1...10 {
        "for \(i)"
        "for \(i)"
    }
    
    if Bool.random() {
        "true statement1"
        "true statement2"
    }
    
    if let header1 = header(true) {
        header1
    }
    
    if let header1 = header(false) {
        header1
    } else {
        "ㅠㅠㅠㅠㅠㅠ"
        "ㅎㅎㅎㅎㅎㅎ"
    }
    
    if let header1 = header(true) {
        header1
    } else {
        "ㅁㄴㅇㄹㅁㄴㅇㄹ"
        "123ㄷㅂㄱㅅㅂㅈ"
    }
    
    if false {
        "aaaaaaaaaa"
    }
    
    switch 3 {
    case 1:
        "ONE"
    case 2:
        "TWO"
    case 3:
        "THREE"
    case 4:
        "FOUR"
    default:
        "DEFAULT"
    }
    
    if #available(iOS 16.0, *) {
        Maker.make()
    } else {
        "asdfqwer"
    }
    
    "<p>This is a sample HTML page.</p>"
    "</body>"
    "</html>"
}
print(html)
```

</details>



## References

* [Swift Documentation - Advanced Operators - Result Builders](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/advancedoperators/#Result-Builders)
* [Swift Documentation - Attributes - resultBuilder](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/attributes/#resultBuilder)
* [Write a DSL in Swift using result builders - WWDC21 - Videos - Apple Developer](https://developer.apple.com/wwdc21/10253)
* [Swift Evolution SE-0289](https://github.com/apple/swift-evolution/blob/main/proposals/0289-result-builders.md)
