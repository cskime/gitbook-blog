---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# \[SwiftUI] Line Height

* SwiftUI는 `lineSpacing(_:)` modifier를 통해 line spacing만 변경할 수 있음
* `{Line Height} = {Line Spacing} + {Font Height}` 이므로, 설정하고자 하는 line height에서 font height을 뺀 값을 line spacing으로 설정하면 된다.
* Font height은 `UIFont`를 사용해서 얻을 수 있다.

## Code

```swift
// 1. UIFont 생성
let uiFont = UIFont(name: "SomeFont", size: 16)!

// 2. 설정하려는 line height으로부터 line spacing 구하기
let lineHeight = 32
let lineSpacing = lineHeight - uiFont.lineHeight

// 3. SwiftUI View에 적용
Text("Some Text")
    .font(Font(uiFont))
    .lineSpacing(lineSpacing)
```

View modifier로 만들어서 사용할 수 있다.

```swift
struct LineHeight: ViewModifier {
    let lineHeight: CGFloat
    let font: UIFont
    
    init(_ lineHeight: CGFloat, _ font: UIFont) {
        self.lineHeight = lineHeight
        self.font = font
    }
    
    func body(content: Content) -> some View {
        content
            .font(Font(font))
            .lineSpacing(lineHeight - font.lineHeight)
    }
}

extension View {
    func lineHeight(_ lineHeight: CGFloat, with font: UIFont) -> some View {
        modifier(LineHeight(lineHeight, font))
    }
}

Text("Hello, World!")
    .lineHeight(24, with: customFont)
```

## 참고

{% embed url="https://stackoverflow.com/questions/61705184/how-to-set-line-height-for-a-single-line-text-in-swiftui" %}

{% embed url="https://developer.apple.com/documentation/swiftui/viewmodifier" %}
