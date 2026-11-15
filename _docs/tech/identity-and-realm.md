---
title: 익명 ID 와 realm
section: 기술
section_url: /tech/
updated: 2026-11-15
---

에듀롬 단말에는 ID 를 두 번 넣습니다. 바깥에 보이는 익명 ID(outer identity)는 요청이 갈 기관을 정하고, 터널 안의 ID(inner identity)는 실제 로그인에 쓰입니다. 두 ID 의 기관 도메인이 다르면 로그인은 실패하고, 비밀번호가 엉뚱한 기관 서버에 남을 수 있습니다.

## 익명 ID 와 ID 의 역할

| | 익명 ID (outer identity) | ID (inner identity) |
|---|---|---|
| 예 | `anonymous@example.ac.kr` | `hong@example.ac.kr` + 비밀번호 |
| 들어가는 자리 | 맨 바깥, 암호화 없음 | 서버 인증서로 연 암호 터널 안 |
| 하는 일 | `@` 뒤 realm 으로 갈 곳을 정함 | 실제 로그인 |
| 볼 수 있는 곳 | 방문 기관, 국내 NRO · RO, 해외 중계 서버 | 소속 기관 인증 서버만 |

로밍 중에 방문 기관과 중계 서버가 보는 것은 익명 ID 뿐입니다. 그래서 `@` 앞은 `anonymous` 로 감추고 `@` 뒤만 소속 기관으로 맞추면 됩니다. 익명 ID 를 비워 두면 실제 ID 가 바깥에도 실리지만 요청은 바르게 갑니다.

ID 는 반드시 `아이디@기관도메인` 전체를 씁니다. realm 이 없으면 방문 기관은 요청을 어디로 보낼지 모릅니다(RFC 7542).

## 기관 도메인 불일치 시 동작

해외 기관을 오가는 연구자는 나라마다 계정이 생깁니다. 예를 들어 국내 `hong@example.ac.kr` 과 해외 `hong@example.edu` 가 있을 때 익명 ID 는 국내, ID 는 해외 계정으로 넣으면 다음 일이 일어납니다.

1. 요청은 익명 ID 의 `@` 뒤를 따라 국내 기관으로 갑니다.
2. 터널은 국내 기관 서버에서 풀리고, 그 서버가 해외 계정 ID 와 비밀번호를 받아 봅니다.
3. 국내 기관에는 없는 계정이므로 로그인은 실패합니다.

암호 터널은 터널을 연 서버까지만 보호합니다. 그 서버에서 터널 안 내용이 그대로 풀리므로, TTLS-PAP 라면 비밀번호가 평문으로, PEAP 라면 비밀번호의 해시가 그 기관 인증 서버에 도착합니다. 실패 기록과 함께 로그에 남을 수 있습니다.

NRO 인증 로그에서 실제로 이런 사례를 확인했습니다. 해외 현지 세 기관에서 여덟 번 시도한 요청이 매번 해외 중계 서버와 국내 NRO 를 거쳐 국내 기관까지 왔다가 모두 거절됐습니다. 익명 ID 자리에 `anonymous` 대신 실제 ID 를 넣어, 그 ID 가 국내외 중계 서버 로그에도 남았습니다.

이용자에게는 이렇게 안내합니다. 익명 ID 와 ID 는 같은 기관 도메인으로 맞춥니다. 계정이 둘이어도 하나만 고르면 되고, 어느 계정이든 전 세계에서 쓸 수 있습니다.

## IdP 설정 점검: 타 기관 ID 재전송 차단

위 사례에서 비밀번호가 바깥으로 더 퍼지지 않으려면 IdP 설정이 막아야 합니다. 터널 안의 ID 가 우리 realm 이 아닌데 IdP 가 그 요청을 다시 밖으로 프록시하면, 다른 기관 사용자의 ID 와 비밀번호가 또 다른 서버로 넘어갑니다. FreeRADIUS 의 기본 inner-tunnel 은 realm 을 다시 찾으므로, DEFAULT realm 이 있으면 요청이 밖으로 나갑니다.

inner-tunnel 의 `authorize` 맨 앞에 다음을 둡니다.

```text
if (&User-Name !~ /@(.+\.)?example\.ac\.kr$/i) {
	reject
}
update control {
	&Proxy-To-Realm := LOCAL
}
```

바깥 ID 와 안쪽 ID 의 realm 이 다르면 거절하는 정책도 함께 권장합니다.

## anonymous@realm 로그의 해석

익명 ID 를 바르게 설정한 단말은 바깥에 `anonymous@realm` 만 보냅니다. 그래서 방문 기관과 NRO, 중계 서버 로그에는 같은 `anonymous@realm` 이 끝없이 찍힙니다. 성공이 쌓이면 「같은 ID 로 여러 명이 접속한다」, 실패가 쌓이면 「누가 대입 공격을 한다」처럼 보이지만 정상입니다. 실제 ID 는 그 realm 의 홈 IdP 만 볼 수 있습니다. 「해킹 시도 같다」는 문의가 들어오면 먼저 익명 ID 인지 확인합니다.

사용자를 나눠 보려면 홈 IdP 가 CUI(Chargeable-User-Identity, RFC 4372, 사용자마다 다른 가명 값)를 돌려주도록 합니다. 방문 기관은 실제 ID 를 모르고도 같은 사용자인지 구분할 수 있습니다. GeCS v2 는 IdP 와 SP 가 CUI 를 지원하도록 정합니다(A.6, B.10).

## 요청에 포함되는 부가 정보

익명 ID 를 쓰더라도 RADIUS 요청에는 다른 정보가 함께 실립니다.

| 속성 | 담는 것 | 전달 범위 |
|---|---|---|
| Calling-Station-Id | 단말 MAC 주소 | 중계 서버와 홈 IdP 까지 |
| Called-Station-Id | AP MAC 주소와 SSID | 중계 서버와 홈 IdP 까지 |
| Operator-Name | 방문 기관 도메인, 예: `1example.ac.kr` | 중계 서버와 홈 IdP 까지 |

Calling-Station-Id 로 익명 ID 뒤의 단말을 구분하고, MAC 주소 앞부분(OUI)으로 제조사를 짐작할 수 있습니다. 단말이 무작위 MAC 을 쓰면 값이 바뀝니다. 위치와 기록에 관한 내용은 [다중캠퍼스 위치와 감사 기록]({{ '/tech/location-logging/' | relative_url }})에서 다룹니다.
