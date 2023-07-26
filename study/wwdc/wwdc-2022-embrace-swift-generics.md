---
description: Write generic code using some and any type
---

# \[WWDC 2022] Embrace Swift Generics

## Abstraction

Detail idea를 분리하는 것

```swift
// before
let startRadians = startAngle * .pi / 100.0
let endRadians = endAngle * .pi / 100.0

// after
func radians(from degrees: Double) -> Double {
    degrees * .pi / 100.0
}
let startRadians = radians(from: startAngle)
let endRadians = radians(from: endAngle)
```

## Polymorphism

하나의 abstraction code가 concrete type에 따라 다르게 동작하도록 만드는 것

### Ad-Hoc Polymorphism

```swift
struct Cow {
    func eat(_ food: Hey)
}

struct Horse {
    func eat(_ food: Carrot)
}

struct Chicken {
    func eat(_ food: Grain)
}
```

* Function overloading 사용
* 단점
  * Boilerplate code가 늘어남

### Subtype Polymorphism

```swift
// Supertype
class Animal {
    func eat(_ food: Any) {
        fatalError("Subclass must implement 'eat'.")
    }
}

// Subtype
class Cow: Animal {
    override func eat(_ food: Any) {
        guard let food = food as? Hay else { 
            fatalError("Cow cannot eat \(food)")
            return 
        }
    }
}

class Horse: Animal {
    override func eat(_ food: Any) {
        guard let food = food as? Carrot else {
            fatalError("Horse cannot eat \(food)")
            return
        }
    }
}

class Chicken: Animal {
    override func eat(_ food: Any) {
        guard let food = food as? Grain else {
            fatalError("Chicken cannot eat \(food)")
            return
        }
    }
}
```

* 상속을 통한 overriding 사용
* 단점
  * Supertype의 필요하지 않은 기능들까지 가져와야 하고, 다른 subtype들과 공유해야 함
  * `eat(_:)`을 실수로 override 하지 않았다는 것을 runtime에만 알 수 있음
  * `eat(_:)`에 다른 type을 넣을 때 의도하지 않은 동작이 실행될 수 있음을 runtime에만 알 수 있음

### Parametric Polymorphism

```swift
class Animal<Food> {
    func eat(_ food: Food) {
        fatalError("Subclass must implement 'eat'.")
    }
}

class Cow: Animal<Hay> {
    override func eat(_ food: Hay) {}
}

class Horse: Animal<Carrot> {
    override func eat(_ food: Carrot) {}
}

class Chicken: Animal<Grain> {
    override func eat(_ food: Grain) {}
}
```

* Type parameter(`Food`)를 사용해서 type safe하게 구현
* 단점
  * 구체적인 type을 superclass 수준까지 알려야 함. 하지만, 그것이 main behavior가 아님
  * Type parameter가 많아지면 boilerplate code가 만들어짐

### Polymorphism using Protocol

```swift
protocol Animal {
    associatedtype Feed: AnimalFeed
    func eat(_ food: Feed)
}

struct Cow: Animal {
    func eat(_ food: Hay) {}
}

struct Horse: Animal {
    func eat(_ food: Carrot) {}
}

struct Chicken: Animal {
    func eat(_ food: Grain) {}
}
```

* Protocol의 `associatedtype`을 사용해서 detail type 없이 type safe하게 사용할 수 있음
* 사용하는 concrete type이 해당 protocol을 준수하는지 compile time에 알 수 있음

## Write a generic code

특정 protocol을 conform하는 type의 값만 허용하도록 generic code 작성

{% hint style="info" %}
**용어**

* Opaque type : 특정 concrete type의 추상 type
* Concrete type : 실제 동작을 결정하는 구체 type
* Underlying type : Opaque type이 추상화 하고 있는 실제 type
* Type erasure
  * 서로 다른 concrete type을 하나의 같은 type으로 표현하는 것
  * Compile time에는 concrete type을 알 수 없고, runtime에만 알 수 있음
{% endhint %}

### where

```swift
struct Farm {
    func feed<A>(_ animal: A) where A : Animal {}
}
```

* `where`는 강력한 방법이지만, 코드가 더 복잡해 보이게 만드는 단점이 있음

### some

```swift
struct Farm {
    func feed(_ animal: some Animal)
}
```

* 복잡도를 낮추고, parameter list에서 정보를 바로 확인할 수 있으므로 직관적이다.
* 어떤 protocol을 conform하는 concrete type을 추상화한 **opaque type**
* Scope 안에서 `some` type의 값에 접근할 때 마다 **항상 동일한 underlying type** 값을 갖는 것이 보장된다.
  * Fixed underlying type

```swift
// 1.
var animal: Animal = Horse()
animal = Chicken()  // ❌

// 2.
func feed(_ animal: some Animal)
feed(Horse())    // ✅
feed(Chicken())  // ✅

// 3.
let cows: [some Animal] = [Cow(), Cow(), Cow()]  // ✅
let animals: [some Animal] = [Cow(), Horse(), Chicken()]  // ❌
```

1. 초기화한 type과 다른 concrete type으로 값을 변경할 수 없음
2. 함수 parameter에 `some`을 사용하는 경우, 함수 실행 scope에서는 전달된 parameter 값이 변경되지 않을 것이므로 함수에 매번 다른 값을 전달하는 것은 가능
3. 같은 protocol을 conform하는 서로 다른 concrete type 값을 하나의 배열(`[some Type]`)에 담을 수 없음

### any

```swift
struct Farm {
    func feedAll(_ animals: [any Animal])
}
```

* `Animal` protocol을 conform하는 모든 concrete type을 parameter로 전달할 때는 `some`을 사용할 수 없음. (`some`은 underlying type이 고정되어 있으므로)
* `any`는 memory에 서로 다른 type들을 유연하게 저장할 수 있음
  * `any` type has a specific representation in memory to allow for flexible storage.
  * `any` has a same static type, but different dynamic types.
  * The static `any` type can dynamically store any concrete types.
  * 크기가 충분히 작은 값들은 직접 저장하고, 크기가 큰 값들은 다른 곳에 저장한 뒤 주소를 참조한다.
* `Animal` protocol을 conform하는 서로 다른 concrete type들을 array에 같이 담으려면 `any`를 사용해야 한다.

```swift
struct Farm {
    func feed(_ animal: some Animal) {
        let crop = animal.plantType.grow()
        let produce = harvest(crop)
        animal.eat(produce)
    }
    
    func feedAll(_ animals: [any Animal]) {
        for animal in animals {
            feed(animal)
        }
    }
}
```

* **Unboxing**
  * Compiler가 `any` type에 들어있는 underlying type 값을 꺼내서 `some` type으로 직접 전달하는 방법
  * `any` type 값은 type이 지워진 상태(type erasure)이므로 associated type을 비롯한 타입 정보를 알 수 없는 상
  * `some` type은 scope 안에서 underlying type이 고정되어 있으므로 타입 정보를 알고 있는 상태
  * `any` type 자체로는 타입 정보를 알 수 없으므로, compiler를 통해 `some` type으로 변환해서 사용

### some vs. any

|             Some             |                Any               |
| :--------------------------: | :------------------------------: |
|  Holds a fixed concrete type | Holds an arbitrary concrete type |
| Guarantees type relationship |     Erases type relationship     |

_**기본적으로**** ****`some`****을 우선 사용하고, 임의의 concrete type value를 저장할 때**** ****`any`****를 사용한다.**_
