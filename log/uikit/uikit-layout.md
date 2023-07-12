---
description: iPad를 외부 모니터에 연결해서 사용할 때, 잘못된 화면 크기를 사용해서 layout이 깨지는 문제
---

# \[UIKit] 외부 모니터 연결 시 Layout 버그

## 문제 상황

* iPad로 앱을 사용하는 사용자가 외부 모니터로 미러링 할 때, layout이 잘못 그려져서 앱을 정상적으로 이용할 수 없음

## 원인 파악

* Layout이 깨질 때, content들이 더 넓은 영역에 그려지고 있음
* View hierarchy debugging을 통해 layout이 깨질 때 content 크기가 외부 모니터의 screen size로 그려지는 것을 확인
* Content를 그리는 곳은 `UIScrollView`에 frame과 scale을 사용해서 content 크기를 계산하고 있음
*   계산할 때 `UIWindow` 인스턴스에 접근하여 `UIScreen`의 size를 가져오는데, 이 때 window 인스턴스를 window array의 맨 앞 element로 사용

    <pre class="language-swift"><code class="lang-swift"><strong>let windowScene = UIApplication.shared.connectedScenes.first as? UIWindowScene
    </strong>let screenSize = windowScene?.screen.bounds.size ?? .zero
    </code></pre>
* 이 때, 외장 모니터의 window 인스턴스가 사용된다면 문제가 발생한다.
  * 외부 display를 연결하면 window가 두 개 이상 생성되는데, 이 때는 위 방법을 사용하면 특정 view가 표시되지 않는 screen size를 가져올 위험이 있다.
  * iOS 12 이하에서는 `UIScreen.main`을 사용해서 device screen을 가져오는 것이 비교적 간단했다.
  * iOS 13부터는 `UIWindowScene`을 통해서 `UIScreen`에 접근할 수 있는데, `connectedScene`은 `Set` type이므로 순서가 보장되지 않는다. `UIApplication.shared.connectedScene.first`로 가져오는 `UIWindowScene`이 원하는 window가 아닐 수 있다.

## 해결

* 외장 모니터로 미러링할 때, screen size를 외장 모니터가 아닌 iPad의 size로 사용해야 함
* `UIScreen`에 접근할 때 **현재 content view가 실제로 그려지고 있는** window를 통해서 가져와야 함
*   특정 view에서 screen size를 사용하여 content layout을 계산한다면, 해당 view의 hierarchy에 있는 `window`에 접근해서 screen size를 가져와야 한다. 이 screen은 현재 view에 연결된 screen이므로 우리가 원하는 screen이다.

    <pre class="language-swift"><code class="lang-swift"><strong>// UIView subclass에서
    </strong>let screenSize = window?.windowScene?.screen.bounds.size ?? .zero

    // UIViewController subclass에서
    let screenSize = view.window?.windowScene?.screen.bounds.size ?? .zero
    </code></pre>
*   iOS 12 이하에서도 `UIWindow`를 통해 screen에 접근하는 방법이 더 안전한 방법이었다.

    ```swift
    // UIView subclass에서
    let screenSize = window?.screen.bounds.size ?? .zero

    // UIViewController subclass에서
    let screenSize = view.window?.screen.bounds.size ?? .zero
    ```
