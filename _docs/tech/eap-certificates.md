---
title: RADIUS 서버 인증서와 CA
section: 기술
section_url: /tech/
updated: 2026-11-15
---

에듀롬 단말은 비밀번호를 보내기 전에 인증 서버가 진짜 소속기관 서버인지 확인해야 합니다. 그 확인의 기준이 CA 인증서와 서버 이름입니다. 이 문서는 인증서가 하는 일, 웹(HTTPS)과 다른 점, 기관이 준비하고 공개할 것을 정리합니다.

## 인증 절차

에듀롬은 IEEE 802.1X(WPA2 · WPA3-Enterprise) 인증을 씁니다. 단말이 ID 와 비밀번호를 보내면 AP 와 무선 컨트롤러는 판단하지 않고 인증 서버(RADIUS)로 넘깁니다. 방문 기관은 ID 의 `@` 뒤에 있는 realm(기관 도메인)만 보고 요청을 국가 로밍 서버(NRO · RO)를 거쳐 소속기관 인증 서버(IdP)로 보냅니다.

1. 방문자 단말 (`hong@example.ac.kr`)
2. 방문 기관 Wi-Fi (SP)
3. 국가 로밍 서버 (NRO · RO)
4. 소속 기관 인증 서버 (IdP): 승인 또는 거절을 같은 길로 되돌려 보냅니다

비밀번호는 단말과 소속기관 서버 사이에 만든 암호 터널(TLS) 안에서만 오갑니다. 이 터널을 열 때 서버가 보내는 것이 서버 인증서입니다. 허가가 나면 사용자는 인터넷만 쓰고, 인증망과 기관 업무망에는 접근하지 못합니다.

참고: RFC 7593 (The eduroam Architecture for Network Roaming), RFC 7542 (NAI), RFC 2865 (RADIUS), RFC 3579 (RADIUS 의 EAP 지원), RFC 3748 (EAP), RFC 5281 (EAP-TTLS)

## 인증서의 역할

인증서는 X.509 형식의 전자 증명서입니다. CA(Certificate Authority, 인증기관)가 「이 공개키는 이 서버의 것」이라고 서명합니다. 목적은 HTTPS 와 같아서, 사용자가 아니라 서버가 위조되지 않았는지 확인합니다.

| | HTTPS 웹 | 에듀롬 (802.1X) |
|---|---|---|
| 확인하는 서버 | 웹 서버 | 소속 기관 RADIUS 서버 |
| 서버 이름의 기준 | 주소창의 도메인 | 기관이 안내한 서버 이름 |
| 믿을 CA | 브라우저에 내장된 목록 | 기관이 안내한 CA |
| 사용자 확인 | 로그인 | ID · 비밀번호 (터널 안) |

에듀롬에는 주소창이 없습니다. 그래서 CA 와 서버 이름을 단말에 직접 지정해야 확인이 이루어집니다.

### CA 인증서와 서버 인증서의 구분

| | CA 인증서 | 서버 인증서 |
|---|---|---|
| 무엇 | 서명한 기관의 인증서, 신뢰의 기준 | RADIUS 서버의 신분증, CA 가 서명 |
| 유효기간 | 10년 이상 | 1~2년 |
| 단말에 지정 | 지정한다 | 지정하지 않는다 |

단말은 두 가지를 확인합니다. 서버 인증서에 지정한 CA 의 서명이 있는지, 서버 이름(CN, SAN)이 안내한 값과 같은지입니다. 서버 인증서는 자주 바뀌지만 CA 가 같으면 단말 설정은 그대로 둘 수 있습니다. 반대로 서버 인증서 자체를 믿게 해 두면 갱신할 때마다 접속이 끊기고 「신뢰하시겠습니까?」가 다시 뜹니다.

RADIUS 인증서도 HTTPS 인증서와 같은 X.509 형식이므로 서로 호환됩니다.

## 웹 브라우저의 자동 검증

웹 서버도 접속할 때마다 인증서를 보내지만, 브라우저는 「신뢰하시겠습니까?」를 묻지 않습니다. 운영체제와 브라우저의 설치 파일 안에 루트 CA 목록이 들어 있기 때문입니다. 서버 인증서에서 서명을 따라 올라가 목록에 있는 루트 CA 에 닿으면 통과하고, 주소창의 도메인과 인증서 이름이 같은지도 자동으로 확인합니다.

```text
루트 CA (운영체제 · 브라우저에 내장)
  └─ 서명 ─ 중간 CA
              └─ 서명 ─ 웹 서버 인증서 (www.example.com)
```
{: .diagram}

이 목록에 들어가기는 매우 어렵습니다. 루트 CA 는 CA/Browser Forum 기본 요건(Baseline Requirements)을 지키고, 매년 외부 감사(WebTrust · ETSI)를 받고, 발급한 인증서를 공개 로그(Certificate Transparency)에 기록하고, 잘못 발급하면 공개 사고 보고를 해야 합니다. 신청부터 포함까지 보통 수년이 걸리고, 규칙을 어기면 목록에서 빠집니다(2018년 Symantec, 2024년 Entrust).

- [Mozilla CA Certificate Program](https://wiki.mozilla.org/CA)
- [Microsoft Trusted Root Program](https://learn.microsoft.com/security/trusted-root/program-requirements)
- [Apple Root Certificate Program](https://www.apple.com/certificateauthority/ca_program.html)
- [Chrome Root Program](https://g.co/chrome/root-policy)
- [Android 시스템 CA 저장소](https://android.googlesource.com/platform/system/ca-certificates)
- [CCADB (Common CA Database)](https://ccadb.org)

## 에듀롬에서 자동 검증이 불가능한 이유

- 접속 전의 단말은 IP 주소가 없고, 인증 서버에 직접 접속하지 않습니다. AP 와 로밍 서버를 거쳐 건네받은 인증서만 봅니다.
- 주소창이 없어 단말은 어느 서버와 이야기해야 하는지 모릅니다. 서버 IP 나 주소로 대조할 수 없습니다.
- 공인 CA 를 믿게 하면 Let's Encrypt 처럼 누구나 몇 분 만에 자기 도메인 인증서를 받는 CA 까지 함께 믿게 됩니다. 서버 이름을 정확히 넣어야만 막을 수 있습니다.

그래서 에듀롬 전용 사설 CA 를 권고합니다. 기관 인증 서버에만 발급하고, 10년 이상 유지하고, 그 CA 를 안내 페이지에 공개합니다.

### 웹 공인 인증서 유효기간 단축

| 발급일 | 공인 CA 서버 인증서 최대 유효기간 |
|---|---|
| 2026. 3. 14. 까지 | 398일 |
| 2026. 3. 15. 부터 | 200일 |
| 2027. 3. 15. 부터 | 100일 |
| 2029. 3. 15. 부터 | 47일 |

2025년 4월 CA/B Forum 에서 통과한 일정입니다([Ballot SC-081v3](https://cabforum.org/2025/04/11/ballot-sc081v3-introduce-schedule-of-reducing-validity-and-data-reuse-periods/)). 웹(HTTPS) 공인 인증서에만 적용되고 RADIUS 인증서에는 이런 국제 기준이 없습니다. 다만 웹용으로 산 공인 인증서를 RADIUS 에 그대로 쓰면 이 주기를 따라 서버 인증서가 바뀝니다.

### 연결 방식별 신뢰 대상

| 연결 방식 | 단말이 믿는 것 | 서버 인증서 갱신 때 |
|---|---|---|
| 프로파일 설치 (CA + 서버 이름, 운영체제 무관) | CA | 그대로 접속 |
| 안드로이드 「시스템 인증서 사용」 + 도메인 | 시스템 CA 목록 (공인 CA 만) | 그대로 접속 |
| iOS · macOS 수동 「신뢰」 | 서버 인증서 (CA 가 있어도) | 다시 묻는다 |
| Windows 11 수동 「연결」 | 서버 인증서 또는 루트 지문 (공식 문서가 모호함) | 시험 필요 |
| 안드로이드 「처음 사용 시 신뢰」 | 서버 인증서 (루트를 보내지 않으면) | 끊긴다 |

사설 CA 도 수동 연결이면 똑같습니다. 차이는 갱신 주기입니다. 공인 웹 인증서는 200일에서 47일로 줄어들고, 사설 CA 의 서버 인증서는 기관이 825일 이하에서 직접 정할 수 있습니다. 운영체제별 설정은 [운영체제별 서버 확인 설정]({{ '/tech/client-validation/' | relative_url }})에서 다룹니다.

## 「CA 미사용」의 실제 의미

PEAP · EAP-TTLS 서버에는 서버 인증서와 개인키, 그리고 그 인증서를 발급한 CA 인증서(체인)가 함께 설치돼 있습니다. 서버는 접속 때마다 서버 인증서와 체인을 단말에 보냅니다. 자체 서명 인증서라면 그 인증서 자신이 CA 입니다. FreeRADIUS 라면 `mods-available/eap` 의 `certificate_file` 과 `ca_file` 에 들어 있습니다.

「CA 미사용」「인증서 사용 안 함」은 단말이 확인하지 않는다는 뜻입니다. 같은 SSID 를 띄운 가짜 AP 도 그대로 통과합니다.

서버 인증서의 발급자(Issuer)를 확인해 그 CA 인증서를 찾고, 지문을 구해 함께 게시합니다.

```sh
openssl x509 -in server.pem -noout -issuer -subject -dates
openssl x509 -in ca.pem -noout -fingerprint -sha256
```

## 업체 기본 인증서 사용의 위험

일부 인증 제품은 설치할 때 제조사가 넣어 둔 기본 인증서를 그대로 씁니다. 그러면 같은 제품을 산 기관들이 같은 개인키, 같은 일련번호, 같은 발급 시각의 인증서를 쓰게 됩니다. 다른 기관이 가진 개인키로 우리 기관 서버를 흉내 낼 수 있다는 뜻입니다. 오래된 기본 인증서는 RSA 1024비트처럼 지금 기준에 못 미치는 키를 쓰고 유효기간이 수십 년인 경우도 있습니다.

설치할 때 기관마다 새 키를 만드는 국산 제품도 많습니다. 문제는 제품이 아니라 설치 방식입니다. 업체에 「우리 기관 전용 키와 인증서로 바꿔 달라」고 요청합니다. 보통 유지보수 계약 범위입니다.

바꾸면 CA 도 바뀝니다. 지금 바르게 설정한 사용자도 프로파일을 지우고 다시 설정해야 하므로, 교체 날짜와 새 CA, 지문을 미리 공지합니다.

## 유효기간 권장 기준

| | 권장 |
|---|---|
| 에듀롬 전용 사설 CA | 10~20년. 바꾸면 모든 사용자가 프로파일을 다시 설정한다 |
| 서버 인증서 | 825일(약 2년 3개월) 이하. CA 가 같으면 바꿔도 단말 설정은 그대로다 |

825일은 Apple 이 2019년 7월 1일 이후 발급한 TLS 서버 인증서에 둔 기준입니다([Requirements for trusted certificates in iOS 13 and macOS 10.15](https://support.apple.com/en-us/103769)). GÉANT 위키는 Apple 단말이 825일을 넘는 인증서를 믿지 않는다고 적었지만, Apple 공개 소스의 EAP 검증 정책([SecPolicy.c](https://github.com/apple-oss-distributions/Security/blob/main/OSX/sec/Security/SecPolicy.c))에는 그 검사가 없습니다. 정책은 바뀔 수 있으므로 825일 이하가 안전합니다.

| 운영체제 | 서버 인증서 최대 기간 검사 |
|---|---|
| iOS · macOS | EAP 검증 정책에는 없다. 825일 · 398일 검사는 HTTPS 정책에만 있다 |
| 안드로이드 · 리눅스 | wpa_supplicant 는 유효기간 안에 있는지만 본다 |
| Windows | 공식 문서에 최대 기간 제한이 없다 |

Apple 은 서버 인증서에 확장 키 용도(EKU)가 있으면 serverAuth 를 요구합니다. 만료된 서버 인증서는 모든 단말이 거절하므로 만료일을 미리 챙깁니다.

참고: [GÉANT wiki, EAP Server Certificate considerations](https://wiki.geant.org/spaces/H2eduroam/pages/121346323/EAP+Server+Certificate+considerations) · [GÉANT CBP-33 Server Certificate Practices in eduroam](https://archive.geant.org/projects/gn3/geant/services/cbp/Documents/cbp-33_server-certificate-practices-in-eduroam.pdf)

## 기관 공개 항목

참여기관은 안내 페이지에 CA 인증서 파일, 그 SHA-256 지문, 서버 이름을 게시합니다. IdP 는 사용자가 상호 인증으로 IdP 를 확인할 수 있게 해야 하고([GeCS v2](https://eduroam.org/wp-content/uploads/2025/07/eduroam_Compliance_Statement_v2-FINAL.pdf) 2.2), GÉANT 운영 가이드는 CA 와 서버 이름을 사용자가 쉽게 찾을 수 있어야 한다고 정합니다([How to support end users](https://wiki.geant.org/display/H2eduroam/How+to+support+to+end+users)). 사용자에게 서버 검증을 끄도록 안내하는 것은 같은 가이드에서 에듀롬 정책 위반으로 봅니다.

CA 공개는 다른 분야에서도 흔한 일입니다. 은행과 행정전자서명(GPKI) 인증관리센터도 인증기관 인증서를 내려받을 수 있게 공개합니다. 안내 페이지는 [안내 페이지 템플릿](https://github.com/eduroam-kr/onepage-html-site-theme)으로 만들 수 있습니다.
