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

# WKWebView에서 Text 선택 시 Menu 제거

## Context Menu

```swift
class CustomWebView: WKWebView {
    override func canPerformAction(_ action: Selector, withSender sender: Any?) -> Bool {
        false
    }
}
```

## Edit Menu

iOS 16부터 `WKWebView`에서 text를 선택하면 'Search Web' 항목이 있는 edit menu가 표시됨

```swift
class CustomWebView: WKWebView {
    @available(iOS 13.0, *)
    override func buildMenu(with builder: UIMenuBuilder) {
        if #available (iOS 16.0) {
            builder.remove(menu: .lookup)
        }
        super.buildMenu(with: builder)
    }
}
```

## Reference

{% embed url="https://stackoverflow.com/questions/73183251/how-to-remove-search-web-option-for-selected-text-ios-16-xcode14-beta" %}
