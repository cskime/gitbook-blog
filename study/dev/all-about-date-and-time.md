---
description: 날짜와 시간을 다루는 방법
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# All About Date and Time

## 위도와 경도

* [위도(latitude)](https://ko.wikipedia.org/wiki/%EC%9C%84%EB%8F%84)
  * 적도를 기준으로 북쪽 또는 남쪽으로 떨어진 위치 (도(°) 단위)
* [경도(longitude)](https://ko.wikipedia.org/wiki/%EA%B2%BD%EB%8F%84)
  * 본초 자오선을 기준으로 동쪽 또는 서쪽으로 떨어진 위치 (도(°) 단위)
  * 180°E \~ 180°W (동경 180° \~ 서경 180°) 까지의 범위 안에 위치

### 자오선

* [자오선(경선)](https://ko.wikipedia.org/wiki/%EA%B2%BD%EC%84%A0)
  * 북극점과 남극점을 최단 거리로 연결하는 세로선
  * 동경(E, X E, X East) : 자오선의 동쪽
  * 서경(W, Y W, Y West) : 자오선의 서쪽
  * 동경과 서경은 180°까지 있고, 동서경 180°는 일치한다.
  * 경도 15°로 1시간의 시차가 생긴다.
* [본초 자오선(prime meridian)](https://ko.wikipedia.org/wiki/%EB%B3%B8%EC%B4%88\_%EC%9E%90%EC%98%A4%EC%84%A0)
  * 영국 그리니치 천문대를 지나는 자오선 (경도 0°)
  * 정오 (낮 12시)의 기준이 된다.

### 날짜 변경선

* [날짜 변경선(International Date Line)](https://ko.wikipedia.org/wiki/%EB%82%A0%EC%A7%9C\_%EB%B3%80%EA%B2%BD%EC%84%A0)
  * 경도 180°를 기준으로 날짜를 구분하는 선
  * 그리니치 천문대를 기준으로 180°W \~ 180°E 끝에서 날짜가 바뀌도록 기준으로 만든 것

## GMT / UTC / Time Zone / DST

### GMT

* [그리니치 평균시/표준시(GMT, Greenwich Mean Time)](https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%A6%AC%EB%8B%88%EC%B9%98\_%ED%8F%89%EA%B7%A0%EC%8B%9C)
* 그리니치 천문대를 기준으로 하는 기준 시간, 평균시
* 협정 세계시의 기준 시간대

### Etc/GMT

* POSIX 시스템의 시간대 표기법 중 하나
* GMT/UTC와 동일한 시간대를 가리키지만, 표기법에 차이가 있다.
* 예시
  * `Etc/GMT+1`은 GMT보다 1시간 앞서 가는 시간대를 나타낸다.
  * 1시간 앞서가므로, `Etc/GMT+1`은 `GMT-1` 또는 `UTC-1`과 같다.
  * 즉, GMT가 1시간 느린 것.

### UTC

* [협정 세계시(UTC)](https://ko.wikipedia.org/wiki/%ED%98%91%EC%A0%95\_%EC%84%B8%EA%B3%84%EC%8B%9C)
* 1972년 1월 1일부터 시행된 국제 표준시
* ‘Coordinated Universal Time’ 또는 ‘Universal Time Coordinated’의 약자
* 1970년 1월 1일 자정을 기준으로 0ms부터 시간을 계산한다.
* GMT에 국제 원자시와 윤초 보정을 한 것
* GMT와 초의 소수점 단위에서만 차이가 나므로, UTC와 GMT를 혼용한다.

### Time Zone

* [시간대 (Time Zone)](https://ko.wikipedia.org/wiki/%EC%8B%9C%EA%B0%84%EB%8C%80)
* 본초 자오선을 기준으로 지역에 따른 시간 차이를 조정하기 위해 만든 구분선 또는 범위
* 지역 별 경도값에 따라 기준시(GMT)로부터 시간 차이를 계산해서 각 지역마다 시간을 보정한다.
* 시차 : $longitude \times \frac{24}{360}$
* 표기법
  * GMT 기준 : GMT (+/-) 시차
  * UTC 기준 : UTC (+/-) 시차
* 참고
  * [Time zone map](https://www.timeanddate.com/time/map/)

### DST(Daylight Saving Time)

* [일광 절약 시간제(Daylight Saving Time) 또는 Summer Time](https://ko.wikipedia.org/wiki/%EC%9D%BC%EA%B4%91\_%EC%A0%88%EC%95%BD\_%EC%8B%9C%EA%B0%84%EC%A0%9C)
* 하절기에 표준시를 원래 시간보다 한 시간 앞당겨서 사용하는 것
  * 2시부터 summer time을 적용하면, 시간을 3시로 조정해야 하는 것
* 캘리포니아의 경우, `GMT-8` 시간대에 속하지만 summer time을 적용하면 `GMT-7`이 된다.

{% hint style="info" %}
**GMT + 16은 없는 이유**

* 미국 캘리포니아는 `GMT-8` 시간대에 속하고, summer time을 적용하면 `GMT-7` 시간대에 속한다. 이것은 캘리포니아가 그리니치 천문대를 기준으로 서쪽에 있다고 보기 때문인데, 그리니치 천문대의 동쪽으로 쭉 가다 보면 캘리포나이를 만날 것이므로 `GMT+16`이라고 표기할 수 있는지 궁금하다.
* 우선, 시간대는 `GMT-11` \~ `GMT+14` 까지밖에 정의되어 있지 않으므로 `GMT+16`이라고 쓸 수는 없다. 그럼 왜 `GMT+14` 까지 밖에 없는가?
* 180°W, 180°E 에 각각 날짜 변경선을 정해 두었다. 시간대가 변경되는 범위는 이 날짜 변경선 안에서 정해진다. 즉,`GMT+16`은 날짜 변경선 180°E를 넘어야 하므로 존재할 수 없다.
* `GMT+13`, `GMT+14` 등의 시간대는 180°E 기준을 조금 넘어서 있는데, 날짜 변경선에 걸쳐있는 국가들에서 날짜를 어떻게 사용하는지에 따라 보정되면서 생긴 것이다. 예를 들어, 12월 30일을 없애기로 했다면 그에 맞게 날짜 변경선이 달라질 수 있고, 그 국가를 지나지 않는 선을 만들면서 부분적으로 `GMT+12`를 넘어서는 시간대가 정의될 수 있다.
{% endhint %}

## Locale

* UI에 사용자의 언어, 지역 설정, 출력 형식 등을 정의한 문자열
* POSIX 기반 시스템에서는 같은 형식을 공유한다. (Unix, Linux, macOS 등)
* Locale의 형식을 변경하여 출력 및 동작 방식을 변경할 수 있다.

### 형식

```
language_[territory].[codeset][@modifier]
```

|  Element  |           Description           |
| :-------: | :-----------------------------: |
|  Language |     언어 (ISO 639-1 표준 형식을 따름)    |
| Territory | 국가 또는 지역 (ISO 3166-1 표준 형식을 따름) |
|  Codeset  |     인코딩 codeset (e.g. UTF-8)    |

* 한국어로, 한국에서, UTF-8 인코딩을 사용하는 경우
  * `ko_KR.UTF-8`
* 언어와 국가는 별개로 설정할 수 있다.
  * `en_US.UTF-8` : 영어, 미국
  * `en_CA.UTF-8` : 영어, 캐나다

### 종류

1. POSIX Locale
   * [POSIX(Portable Operating System Interface)](https://ko.wikipedia.org/wiki/POSIX)
     * 이식 가능 운영체제 인터페이스
     * 서로 다른 UNIX OS의 공통 API를 정리하여 이식성 높은 유닉스 응용 프로그램을 개발하기 위한 목적으로 IEEE가 책정한 애플리케이션 인터페이스 규격
   * POSIX 시스템의 기본 locale로, 미리 정의된 형식으로 날짜를 출력한다.
   * `en_US_POSIX` : POSIX 규격에서 정의한 locale 설정을 사용한다.
2. C Locale
   * C 언어에서 정의하는 locale
   * POSIX 계열 운영체제에서는 C와 POSIX locale이 혼용됨

## [ISO8601](https://ko.wikipedia.org/wiki/ISO\_8601)

날짜와 시간과 관련된 데이터 교환을 다루는 국제 표준

### 날짜 표기 규정

* 그레고리력(Gregorian) 사용
* 연도 : 최소 4자리 \[`YYYY`] 규정 (0000\~9999년)
* 달 : 2자리 \[`MM`] 규정 (01\~12월)
* 일 : 2자리 \[`DD`] 규정 (01\~31일)
* 날짜 표기법
  * 전체 표기 : `YYYY-MM-DD` 또는 `YYYYMMDD`
  * 날짜가 없을 때 : `YYYY-MM`
  * 연도가 없을 때 : `--MM-DD` 또는 `--MMDD`

### 시간 표기 규정

* 24시간제 시계 시스템 사용
* 시 : 2자리 \[hh] 규정 (00\~24시)
* 분 : 2자리 \[mm] 규정 (00\~59분)
* 초 : 2자리 \[ss] 규정 (00\~60분, 60은 윤년초를 나타내는 용도로만 사용)
* 시간 표기법
  * 전체 표기 : `hh:mm:ss.sss` 또는 `hhmmss.sss`
  * 소수점 없을 때 : `hh:mm:ss` 또는 `hhmmss`
  * 초 없을 때 : `hh:mm` 또는 `hhmm`
  * 초, 분 없을 때 : `hh`

### 날짜 및 시간 혼합 표기 규정

* `T` 문자로 날짜와 시간을 연결해서 표현한다.
* `YYYY-MM-DD'T'hh:mm:ss`

### 시간대 표기 규정

* UTC 시간대 : 마지막에 `Z` 를 붙인다.
  * `YYYY-MM-DDThh:mm:ssZ`
* 이외 시간대 : `+/-hh:mm` 을 붙인다.
  * 전체 표기 : `YYYY-MM-DDThh:mm:ss+hh:mm`
  * 축약 표기 : `YYYY-MM-DDThh:mm:ss+hhmm`
  * 분 생략 : `YYYY-MM-DDThh:mm:ss+hh`

## [RFC3339](https://datatracker.ietf.org/doc/html/rfc3339)

* ISO8601을 인터넷 프로토콜로 어떻게 다룰 것인지 규정한 RFC
* 큰 차이가 없으므로 일반적으로 혼용한다.
  * RFC3339에서는 ‘T’의 생략을 허용하지 않는다.
  * RFC3339에서는 날짜와 시간 사이의 공백을 허용한다.

## 참고

* [TimeZone, Locale, 날짜 표현 형식 개념 확실히 잡아보자!](https://velog.io/@kerri/TimeZone-Locale-DateFormat)
* [한국표준시(KST)에 대한 흥미로운 이야기](https://velog.io/@hiro2474/%ED%95%9C%EA%B5%AD%ED%91%9C%EC%A4%80%EC%8B%9C%EC%97%90-%EB%8C%80%ED%95%9C-%ED%9D%A5%EB%AF%B8%EB%A1%9C%EC%9A%B4-%EC%9D%B4%EC%95%BC%EA%B8%B0)
* [세계 표준시 UTC, GMT와 한국 시간 KST 계산 방법](https://muksteem.tistory.com/entry/%EC%84%B8%EA%B3%84-%ED%91%9C%EC%A4%80%EC%8B%9C-UTC-GMT%EC%99%80-%ED%95%9C%EA%B5%AD-%EC%8B%9C%EA%B0%84-KST-%EA%B3%84%EC%82%B0-%EB%B0%A9%EB%B2%95)
* [IBM: POSIX and Olson time zone formats](https://developer.ibm.com/articles/au-aix-posix/)
* [UTC와 표기법, 그리고 ISO8601, RFC3339 표준](https://ohgyun.com/416)
* [로케일(Locale)이란? 국가 및 언어 설정](https://www.44bits.io/ko/keyword/locale)
