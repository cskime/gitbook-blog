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

# ISO3166

## [ISO3166](https://en.wikipedia.org/wiki/ISO\_3166)

* 전 세계 나라와 부속 영토, 주요 구성 단위 명칭에 고유 부호를 부여하는 국제 표준
* 세 가지 표준으로 구분
  * ISO3166-1 : 국가, 부속 영토 명칭 부호
  * ISO3166-2 : 국가, 부속 영토(provinces or states)의 주요 구성 단위 명칭에 부호 부여
  * ISO3166-3 : ISO3166-1에서 삭제된 부호를 대체하는 부

## [ISO3166-1](https://en.wikipedia.org/wiki/ISO\_3166-1)

* ISO3166-1-alpha2 : 두 자리 표기
* ISO3166-1-alpha3 : 세 자리 표기
* ISO3166-1-numeric : 숫자 표기

<table><thead><tr><th width="149" align="center">Country</th><th align="center">alpha2</th><th align="center">alpha3</th><th align="center">numeric</th></tr></thead><tbody><tr><td align="center">Korea</td><td align="center">KR</td><td align="center">KOR</td><td align="center">410</td></tr><tr><td align="center">United State</td><td align="center">US</td><td align="center">USA</td><td align="center">840</td></tr><tr><td align="center">Japan</td><td align="center">JP</td><td align="center">JPN</td><td align="center">392</td></tr></tbody></table>

## [ISO3166-2](https://en.wikipedia.org/wiki/ISO\_3166-2)

* ISO3166-1 국가 코드에 해당 국가의 지역마다 code가 추가됐다.
* [ISO3166-2:JP](https://en.wikipedia.org/wiki/ISO\_3166-2:JP) : ISO3166-2의 일본 코드
  * JP-31 : Tokyo
  * JP-26 : Kyoto
  * JP-27 : Osaka

## [ISO3166-3](https://en.wikipedia.org/wiki/ISO\_3166-3)

* 네 자리 문자로 표기한다.
* 앞의 두 자리는 **ISO3166-1-alpha2 코드**를 사용한다. (이하 '원래 코드')
* 뒤의 두 자리는 아래 규칙에 따라 정한다.
  1. 국가 이름이 바뀌었을 때, **바뀐 이름의 새로운 ISO3166-1-alpha2 코드가 기존과 같은가**
     * 같으면, `AA` 사용 (e.g. 기존 코드와 바뀐 코드가 모두 `BY`이면 `BYAA`)
     * 다르면, 새 코드 사용 (e.g. 기존 코드가 `BU`이고 바뀐 코드가 `MM`이면 `BUMM`)
  2. 다른 나라로 합병되었을 때, **합병된 나라의 ISO3166-1-alpha2 코드** 사용
     * 'German Democratic Republic'이 'Germany'으로 합병된 경우 ('German Democratic Republic' merged into 'Germany'), `DDDE`
       * German Democratic Republic : `DD`
       * Germany : `DE`
  3. 국가가 분할되었을 때,
     * `HH` 사용
       * 'Czechoslovakia'(`CS`)가 'Czechia'(`CZ`)와 'Slovakia'(`SK`)로 분할된 경우 `CSHH`
     * `--HH`가 다른 코드와 중복되는 경우, `HH` 대신 `XX` 사용
       * 'Serbia and Montenegro'(`CS`)가 'Montenegro'(`ME`)와 'Serbia'(`RS`)로 분할
       * `CSHH`는 다른 코드와 중복되므로 `CSXX`로 사
