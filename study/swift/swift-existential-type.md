---
description: Existential type이란? Existential(실존적)이란 무엇인가?
---

# \[Swift] Existential Type

## Concept

* Existence(실존)
  * 실제로 존재하는 것
  * 사물의 본질이 아닌, 그 사물이 존재하는 그 자체
* Existential(실존적)
  * 주체적 존재로 실재하는 것
* Existential Type
  * 실제 타입을 갖고 있는 타입
  * 실제 존재하는 타입의 '본질'

## Swift Type System에서의 Existential Type

* **Any type, but conforming to protocol `X`**
* Implementation과 interface를 분리한&#x20;

### Variable

```swift
protocol P {}
class A: P {}
class B: P {}

var value: P = A()
```

* **`P` : Existential type**
* `A`, `B` : Record type. (or Concrete Type)

### Method

```swift
protocol P {}

// 1.
func execute<T: P>(parameter: T) {}

// 2.
func execute(parameter: P) {}
```

* Generic method에서, `parameter`에는 `P`를 conform하는 어떤 type들도 들어올 수 있음
* Generic 대신 protocol type을 직접 사용해서 해당 protocol을 conform하는 type들만 받을 수 있음

### Existential Any

* Swift 5.6에서 소개된 개념
* Existential type으로 사용하는 protocol임을 명시적으로(explicitly) 나타내는 키워드

```swift
protocol P {}
protocol A: P {}

var value: any P = A()
func execute(parameter: any P) {}
```

## Reference

* [https://en.wikipedia.org/wiki/Type\_system#Existential\_types](https://en.wikipedia.org/wiki/Type\_system#Existential\_types)
* [https://stackoverflow.com/questions/292274/what-is-an-existential-type](https://stackoverflow.com/questions/292274/what-is-an-existential-type)

