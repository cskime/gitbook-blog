---
description: 일본 지역으로 설정했을 때 오전/오후 표기로 인해 date format이 깨지는 문제
---

# \[iOS] 일본 지역 설정 시 Date Format 오류

## 문제 상황

* 서버가 iOS 앱이 업로드 한 date string을 parsing하지 못하고 error 반환
* Client는 서버가 error를 응답하여 사용자에게 잘못된 UI를 보여줌
* 일본의 일부 사용자에게만 발생하는 문제
* 내부에서 문제가 재현되지 않음

## 원인 파악

* 문제가 발생한 일본 사용자와 화상 콜을 진행하며 원인 파악
* 실제로 업로드되는 데이터를 분석하기 위해, 데이터를 로깅하는 기능이 추가된 테스트 앱을 사용자에게 전달
  * Enterprise 인증서로 빌드하여 사용자가 기기를 등록하지 않고 테스트 앱을 설치할 수 있도록 함
  * Firebase firestore를 사용하여 실시간으로 데이터를 수집하고 조회할 수 있는 환경 구축
* 로그 분석 결과, **일본어 / 일본 지역 / 12시간제** 설정된 iOS device에서 서버로 전송하는 date string이 서버에서 지원하지 않는 포맷으로 업로드됨
  * Expected : `2023-04-12T23:25:33.820Z`
  * Actural : `2023-04-12T午後8:49:58.107Z`
  * 서버에서는 expected format으로 date string을 parsing하는데, 앱에서는 actural format으로 업로드해서 발생하는 문제
* 24시간제로 설정했을 때는 문제가 발생하지 않기 때문에 일부 사용자들에게만 발생하여 문제를 재현하기 어려웠음

## 문제 해결

* 날짜가 언어와 지역에 상관 없이 항상 동일한 포맷으로 업로드 될 수 있도록 수정
* `DateFormatter`의 local 설정을 `en_US-POSIX`로 변경한 뒤 문제 해결
* 여러 국가에 서비스하는 앱에서는 date string을 ISO8601 규격에 맞게 formatting해서 사용하는 것이 안

### Code

```swift
func dateString(from date: Date) -> String {
    let formatter = DateFormatter()
    formatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'"
    formatter.timeZone = TimeZone(identifier: "UTC")
    formatter.locale = Locale(identifier: "en_US_POSIX")
    return formatter.string(from: date)
}
```

* `ISO8601DateFormatter`를 사용할 수도 있지만, 서버에서 요구하는 date format을 정확하게 맞추기 위해 date format을 직접 지정
  * `ISO8601DateFormatter`는 `SSS'Z'`로 formatting 하지 않
* UTC 시간대로 사용하기 위해 UTC timezone 사용
* 지역에 상관 없이 항상 같은 형식으로 formatting하기 위해 `en_US_POSIX` locale 사
* <mark style="color:red;">**`en_US_POSIX`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**locale을 설정한 후 다시 date format을 설정하지 않도록 주의**</mark>

## 참고

* [Broken link](broken-reference "mention")
* [https://developer.apple.com/forums/thread/728922](https://developer.apple.com/forums/thread/728922)
* [https://developer.apple.com/library/archive/qa/qa1480/\_index.html](https://developer.apple.com/library/archive/qa/qa1480/\_index.html)
