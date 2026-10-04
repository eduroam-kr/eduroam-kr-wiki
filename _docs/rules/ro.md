---
title: 로밍 운영기관(RO) 운영 요건
section: 규정
section_url: /rules/
updated: 2026-10-04
version: v1.0
toc: true
---

[국가에듀롬 운영약관]({{ '/rules/provisions/' | relative_url }}) 1.2 가 RO 를 둘 수 있다고 정하지만, RO 가 무엇을 갖추어야 하는지는 적지 않습니다. 약관에 IdP(부록 A)·SP(부록 B)·NRO(부록 C) 의 준수 사항은 있어도 RO 의 것은 없습니다. 이 문서가 그 자리를 채웁니다.

## 누가 지켜야 하나

- **대학 RO** — 한국교육정보화재단(KREN). 2024년 11월부터 대학·대학교를 담당합니다
- 앞으로 설립되는 **부문 RO** — 약관 1.2.1 에 따라 지리적·기능별·성격별로 설립될 수 있습니다

RO 는 자기 부문 안에서 NRO 를 대리합니다. 그래서 NRO 에게 요구되는 것(약관 부록 C)의 대부분이 부문 범위로 좁혀져 그대로 적용됩니다.

대학을 제외한 기관은 RO 없이 NRO 가 직접 담당합니다(약관 1.2.8). 그 기관은 이 문서가 아니라 [고지문 게시 규정]({{ '/rules/notice/' | relative_url }})과 약관 4.5 를 봅니다.

# 기술 요구사항

## 1. RADIUS 기반시설

1.1. RO 는 부문에듀롬기반시설로 RADIUS 프록시를 운영한다. 참여기관과 NRO 양쪽에 붙는다.

1.2. RADIUS 프록시는 이중화한다. 한 대가 멎어도 부문 전체의 인증이 멎지 않아야 한다.

1.3. realm 라우팅은 **명시한 realm 만** 올려보낸다. 정규식으로 `*.ac.kr` 같이 싸잡아 넘기지 않는다. 목록에 없는 realm 을 되돌려 보내면 NRO 와 RO 사이에서 요청이 맴돈다([2017.12. 운영정책 세미나]({{ '/records/2017-12-policy-seminar/' | relative_url }})에서 실제로 겪은 문제다).

1.4. 참여기관이 realm 을 더하거나 뺀 날로부터 **근무일 기준 3일 안에** 자기 라우팅과 NRO 쪽 정보에 반영한다.

1.5. 서버 주소·공유키를 바꾸기 전에 참여기관과 NRO 에 **최소 2주 전** 알린다. 긴급 보안 조치는 예외로 하되 사후에 알린다.

1.6. 구현 예시는 [nro-docker](https://github.com/eduroam-kr/nro-docker) 에 있다.

## 2. Operator-Name

접속 요청이 어느 기관에서 왔는지는 `Operator-Name`([RFC 5580](https://www.rfc-editor.org/rfc/rfc5580) 4.1) 하나로 드러납니다. 이게 없으면 IdP 는 자기 이용자가 어디서 접속했는지 알 수 없고, 사고가 났을 때 되짚을 수 없습니다.

2.1. SP 는 자기가 보내는 Access-Request 에 `Operator-Name` 을 넣는다. 값은 네임스페이스 `1`(REALM) 뒤에 자기 realm 을 붙인 꼴이다.

```text
Operator-Name = "1example.ac.kr"
```

2.2. **기관이 넣지 못하면 RO 가 자기 프록시에서 붙인다.** 장비가 오래되었거나 설정을 바꿀 수 없는 기관이 적지 않다. 그런 기관 때문에 부문 전체의 추적이 끊기지 않게 한다.

2.3. RO 가 붙일 때는 **요청을 보낸 그 기관의 realm** 을 쓴다. RO 자신의 realm 을 쓰지 않는다. 그러려면 RADIUS 클라이언트마다 어느 기관인지 적어 두어야 한다.

2.4. 이미 들어 있는 `Operator-Name` 은 덮어쓰지 않는다. 기관이 제대로 넣은 값을 RO 가 지우면 거짓 정보가 된다.

2.5. RO 는 자기 부문에서 `Operator-Name` 이 빠진 기관을 파악하고, 기관이 직접 넣도록 안내한다. RO 가 대신 붙이는 것은 임시 조치다.

## 3. 로깅

3.1. RO 는 자기 RADIUS 프록시의 인증 로그를 **6개월 이상** 안전하게 보관한다.

3.2. 로그에는 다음을 남긴다 — timestamp, 바깥 identity, realm, 기기 MAC 주소, SP 서버, `Operator-Name`, 인증 결과.

3.3. **비밀번호는 어떤 형태로도 남기지 않는다.**

3.4. NRO 가 사고 조사를 위해 요청하면 근무일 기준 3일 안에 해당 구간의 로그를 제공한다.

# 정책 요구사항

## 4. 연락처와 소통

4.1. RO 는 사람 이름이 아닌 **역할 주소**를 둔다. 담당자가 바뀌어도 주소는 그대로여야 한다.

4.2. 다음 세 창구를 각각 둔다.

| 창구 | 상대 | 쓰임 |
|---|---|---|
| 기술 연락처 | NRO, 다른 RO | 장애, realm 변경, 보안 사고 |
| 참여기관 창구 | 자기 부문의 참여기관 | 가입, 설정 문의, 장애 접수 |
| 공개 지원 연락처 | 최종 이용자 | 웹페이지에 싣는 주소 |

4.3. 근무일 기준 **2일 안에** 응답한다. 보안 사고는 **당일** 응답한다.

4.4. NRO 의 운영 메일링리스트에 가입하고, 자기 부문 참여기관에게도 전달한다.

4.5. 연락처가 바뀌면 바뀐 날 NRO 에 알린다.

## 5. 참여기관 연락처는 NRO 도 가진다

RO 를 통해 가입한 기관이라도 **NRO 가 그 기관의 연락처를 알고 있어야 합니다.** 글로벌에듀롬과 해외 NRO 는 국가 단위로 연락합니다. 보안 사고나 해외에서 온 조회가 들어왔을 때 NRO 가 RO 를 거쳐야만 기관에 닿을 수 있다면, 그 시간만큼 대응이 늦습니다. RO 가 응답하지 못하는 시간대나 상황도 있습니다.

5.1. RO 는 자기 부문 참여기관의 연락처를 NRO 에 전달한다. 약관 4.3 이 참여기관에게 요구하는 것과 같은 범위다 — 기술담당자 2인(하나는 그룹 주소 권장), 보안담당자 1인, 관리담당자 1인.

5.2. 기관이 가입한 날, 그리고 연락처가 바뀐 날 전달한다.

5.3. RO 를 거치는 것은 전달 경로일 뿐이다. **RO 가 연락 창구를 독점하지 않는다.** NRO 는 필요할 때 기관에 직접 연락할 수 있다.

5.4. 연락처는 공개하지 않는다. [eduroam-kr-db](https://github.com/eduroam-kr/eduroam-kr-db) 는 공개 저장소이므로 담당자 개인 주소를 그곳에 올리지 않는다. 전달 경로는 NRO 가 따로 정한다.

## 6. 참여기관 관리

6.1. RO 는 자기 부문의 가입 자격을 약관 2.3·2.4 에 따라 판단한다. 약관보다 느슨하게 적용할 수 없다.

6.2. 참여기관 정보는 [eduroam-kr-db](https://github.com/eduroam-kr/eduroam-kr-db) 에 제출한다. 기관명, realm, 서비스 지역, 안내 페이지 주소(`info_url`), 가입 연도를 포함한다. 담당자 연락처는 5.4 에 따라 여기에 넣지 않는다.

6.3. 참여기관이 약관을 어긴 것을 알게 되면 해당 기관에 알리고 기한을 정해 고치게 한다. 기한 내에 고쳐지지 않으면 NRO 에 알린다.

6.4. 참여기관이 탈퇴하거나 자격을 잃으면 **그날** 라우팅에서 빼고 NRO 에 알린다.

6.5. 참여기관의 안내 페이지가 약관 4.5 와 [고지문 게시 규정]({{ '/rules/notice/' | relative_url }})을 지키는지 **연 1회 이상** 확인한다.

## 7. RO 웹페이지

7.1. RO 는 자기 eduroam 웹페이지를 둔다. 주소는 바뀌지 않는 곳으로 정하고 NRO 에 알린다.

7.2. **HTTPS 로만** 제공한다. HTTP 로 들어온 요청은 HTTPS 로 넘긴다.

7.3. 다음을 게재한다.

(1) 국가에듀롬 운영약관의 준수 — [약관]({{ '/rules/provisions/' | relative_url }}) 링크를 포함한다
(2) 자기 부문의 참여기관 목록 또는 서비스 지역 지도 — 각 기관의 안내 페이지로 링크한다
(3) 가입 절차와 가입 문의 창구
(4) 공개 지원 연락처 (4.2 의 셋째)
(5) [대한민국 eduroam 공식 웹사이트](https://eduroam.kreonet.net) 링크
(6) 참여 선언과 공통 고지문 — [고지문 게시 규정]({{ '/rules/notice/' | relative_url }})

7.4. 검색에 걸려야 한다. `<meta name="robots" content="noindex">` 를 두거나 `robots.txt` 로 막지 않는다. 고지문을 읽히지 않게 만드는 것과 같은 일로 본다.

## 8. HTML `<head>` 요건

RO 와 참여기관의 eduroam 페이지는 아래를 갖춥니다. 사람이 읽는 본문만큼이나 기계가 읽는 머리도 맞아야, 검색과 메신저 미리보기에서 그 페이지가 무엇인지 드러납니다.

### 8.1. 필수

| 태그 | 값 |
|---|---|
| `<html lang>` | 국문 쪽은 `ko`, 영문 쪽은 `en` |
| `<meta charset>` | `utf-8` |
| `<meta name="viewport">` | `width=device-width, initial-scale=1` |
| `<title>` | `<기관 이름> eduroam 안내` — 기관 이름을 반드시 넣는다 |
| `<meta name="description">` | 한 문장. 누가 쓰는 서비스인지 밝힌다 |
| `<link rel="canonical">` | 이 쪽의 정식 주소 (절대 주소) |
| `<meta name="copyright">` | 아래 공통 메타 그대로 |
| `<link rel="related">` | 아래 공통 메타 그대로 |

### 8.2. 공통 메타 — 글자 그대로

```html
<meta name="copyright" content="eduroam® is a registered trademark of the GÉANT Association. eduroam service in the Republic of Korea is operated by KREONET, the national research network of KISTI.">
<link rel="related" href="https://eduroam.kreonet.net" title="대한민국 eduroam 공식 웹사이트 (KREONET 운영)">
```

국문 쪽과 영문 쪽 모두 이 두 줄을 **같게** 둡니다. 번역하거나 기관 이름을 끼워 넣지 않습니다.

### 8.3. 권고

| 태그 | 값 |
|---|---|
| `<meta property="og:type">` | `website` |
| `<meta property="og:title">` | `<title>` 과 같게 |
| `<meta property="og:description">` | `description` 과 같게 |
| `<meta property="og:url">` | canonical 과 같게 |
| `<meta property="og:image">` | 1200×630 PNG 절대 주소. `og:image:width`, `og:image:height`, `og:image:alt` 를 함께 둔다 |
| `<meta property="og:site_name">` | `<기관 이름> eduroam` |
| `<meta property="og:locale">` | `ko_KR` 또는 `en_US` |
| `<meta name="twitter:card">` | `summary_large_image` |
| `<meta name="color-scheme">` | `light dark` |
| `<link rel="alternate" hreflang>` | 국·영문 두 벌을 둘 때. 서로를 가리키게 한다 |

### 8.4. 통째로 가져다 쓰기

[onepage-html-site-theme](https://github.com/eduroam-kr/onepage-html-site-theme) 의 `index.html` 머리에 위 항목이 모두 들어 있습니다. 포크해서 기관 이름과 주소만 바꾸면 8장 전체를 만족합니다.

```sh
sed -n '/<head>/,/<\/head>/p' index.html
```

바꿀 값은 저장소 `README.md` 의 "수정할 값" 에 정리돼 있습니다.

## 9. 상표와 고지문

9.1. RO 는 eduroam 이름과 로고를 쓸 때 [고지문 게시 규정]({{ '/rules/notice/' | relative_url }})을 지킨다. RO 용 참여 선언 문장이 그 문서에 있다.

9.2. RO 는 eduroam 을 자기 브랜드로 다시 포장하지 않는다. 별도 이름을 붙인 파생 브랜드를 만들지 않는다([연혁]({{ '/history/' | relative_url }}) 2012–2015년 참조).

9.3. RO 는 자기 부문 참여기관이 9.1 을 지키는지 6.5 에 따라 확인한다.

# 점검표

| | 항목 | 근거 |
|---|---|---|
| ☐ | RADIUS 프록시 이중화 | 1.2 |
| ☐ | 명시한 realm 만 라우팅 | 1.3 |
| ☐ | `Operator-Name` 이 모든 요청에 들어감 | 2.1, 2.2 |
| ☐ | 기관 realm 으로 붙이고 기존 값은 보존 | 2.3, 2.4 |
| ☐ | 인증 로그 6개월, 비밀번호 제외 | 3.1, 3.3 |
| ☐ | 역할 주소 세 창구 | 4.1, 4.2 |
| ☐ | 참여기관 연락처를 NRO 에 전달 | 5.1, 5.2 |
| ☐ | 연락처를 공개 저장소에 올리지 않음 | 5.4 |
| ☐ | 참여기관 정보를 eduroam-kr-db 에 제출 | 6.2 |
| ☐ | 참여기관 안내 페이지 연 1회 점검 | 6.5 |
| ☐ | RO 웹페이지 HTTPS, 여섯 항목 게재 | 7.2, 7.3 |
| ☐ | `noindex` 없음 | 7.4 |
| ☐ | `<head>` 필수 여덟 항목 | 8.1 |
| ☐ | 공통 메타 두 줄 그대로 | 8.2 |
| ☐ | 참여 선언과 공통 고지문 | 9.1 |
