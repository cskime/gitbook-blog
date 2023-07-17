# \[Swift] AnyObject

## Overview

* 모든 class가 암시적으로(implicitly) 준수하는 protocol
* Reference type 값을 담는 **existential type**
  * Use when you need the flexibility of an untyped object
  * Use bridged Objectvie-C methods and properties that return an untyped result
  *   Use when you bridges Swift types to an Objective-C class

      ```swift
      let s: AnyObject = "This is a bridged string." as NSString
      print(s is NSString)
      // Prints "true"

      let v: AnyObject = 100 as NSNumber
      print(type(of: v))
      // Prints "__NSCFNumber"
      ```
*   Class, class type, class-only protocol 등을 담을 수 있다.

    ```swift
    class FloatRef {
        let value: Float
        init(_ value: Float) {
            self.value = value
        }
    }

    let x = FloatRef(2.3)
    let y: AnyObject = x
    let z: AnyObject = FloatRef.self
    ```

## Casting

*
*   Type casting operator(`as`, `as?`, `as!`)를 사용해서 specific dynamic type으로 casting 할 수 있다.

    ```swift
    if let message = s as? String {
        print("Successful cast to String: \\(message)")
    }
    ```
*   `switch`에서는 항상 안전하게 casting 할 수 있다.

    ```swift
    let mixedArray: [AnyObject] = [s, v]
    for object in mixedArray {
        switch object {
        case let x as String:
            print("'\\(x)' is a String")
        default:
            print("'\\(object)' is not a String")
        }
    }
    ```

### Swift Value Type → AnyObject

*   Swift value type을 `AnyObject`로 casting 할 수 있다.

    ```swift
    // error
    let array: [AnyObject] = [1, false, "hi"]

    // ok
    let array: [AnyObject] = [1 as AnyObject, false as AnyObject, "hi" as AnyObject]
    ```

    * Swift에서 struct로 구현되어 있는 타입들을 `AnyObject`로 conversion
    * `Int`, `Float`, `Bool` 등
* Objective-C interop에 의해 가능
  * Swift의 value type을 Objective-C의 `__SwiftValue` class로 변환해 준다.
  * Foundation framework에서 Swift의 value type 값을 `NSNumber`, `NSString` 등으로 변환
  * 이 type들은 class로 구현되어 있기 때문에, `AnyObject`로 up casting 될 수 있다.

## Reference

* [https://developer.apple.com/documentation/swift/anyobject](https://developer.apple.com/documentation/swift/anyobject)
* [https://forums.swift.org/t/anyobject/35659](https://forums.swift.org/t/anyobject/35659)
* [swift-existential-type.md](swift-existential-type.md "mention")
