---
title: 다중캠퍼스 위치와 감사 기록
section: 기술
section_url: /tech/
updated: 2026-11-15
---

「어느 캠퍼스, 어느 건물에서 접속했나」는 표준 RADIUS 속성과 무선 컨트롤러의 기본 기능으로 상당 부분 남길 수 있습니다. 장비가 못 하는 부분은 FreeRADIUS 중계 하나와 오픈소스 수집 도구로 채웁니다. 이 문서는 쓸 수 있는 속성, 장비 지원 현황, 기록을 엮는 구성 예를 정리합니다.

## 배경

대학 통폐합으로 여러 캠퍼스와 이기종 무선 장비를 함께 운영하는 기관이 크게 늘었습니다. 「안 된다」는 문의를 추적하려면 어느 캠퍼스, 어느 AP, 어떤 방법으로 들어왔는지 기록이 있어야 합니다. 그런데 무선 컨트롤러 내장 인증이나 인증 어플라이언스를 쓰는 기관은 로그 형식을 바꾸거나 모으기 어렵습니다. 여기서는 제품을 새로 들이지 않고 가진 장비와 오픈소스를 조합해 기록과 감사(audit)를 하는 방법을 다룹니다.

## RADIUS 위치 정보 속성

| 속성 | 담는 것 | 위치 구분 |
|---|---|---|
| Operator-Name (126) | 방문 기관(서비스 제공 기관) 도메인, 예: `1example.ac.kr` | 기관까지 |
| Called-Station-Id | AP MAC 주소와 SSID (RFC 3580 권고 형식) | AP 까지. 건물은 따로 만든 대응표로 |
| NAS-Identifier · NAS-IP-Address | 요청을 보낸 무선 컨트롤러 | 컨트롤러까지 |
| Calling-Station-Id | 단말 MAC 주소 | 위치 아님. 무작위 MAC 이면 바뀜 |

Called-Station-Id 에 건물 이름을 끼워 넣는 설정은 제조사마다 다르고 표준이 아닙니다. 캠퍼스, 건물, 층을 적는 표준 자리는 RFC 5580(2009)이 정의한 위치 속성(126 ~ 132)입니다. 인증 요청과 세션 기록에 접속 지점의 위치를 담으며, 형식은 주소형(Civic, RFC 4776)과 좌표형(위도 · 경도 · 고도) 두 가지입니다.

### Operator-Name

Operator-Name 은 요청을 보낸 방문 기관을 적는 속성입니다. 값은 이름 공간 번호와 기관 도메인을 붙여 쓰며, `1example.ac.kr` 의 1 은 DNS 도메인 이름이라는 뜻입니다. Called-Station-Id 와 Calling-Station-Id 는 중계 서버와 홈 IdP 까지 전달되지만 어느 기관에서 온 요청인지는 알려 주지 않습니다. AP MAC 이 어느 건물인지도 그 기관만 압니다. Operator-Name 이 그 빈자리를 채웁니다.

FreeRADIUS 에는 이 정책이 들어 있지만 기본 설정에서는 주석 처리돼 있습니다(`policy.d/operator-name`). 기본 인증 로그에는 남지 않으므로 상세 로그를 따로 기록해야 보입니다. GeCS v2 는 SP 가 Operator-Name 을 넣도록 정합니다(B.10).

참고: [RFC 5580](https://www.rfc-editor.org/rfc/rfc5580) · [RFC 4776](https://www.rfc-editor.org/rfc/rfc4776) · [Jisc, Operator-Name](https://community.jisc.ac.uk/node/297)

### 주소형(Civic) 위치 항목

| CAtype | 뜻 (RFC 4776) | 대학 예 |
|---|---|---|
| (머리) | 국가 코드 | `KR` |
| 1 · 3 | 시 · 도, 시 · 군 · 구 | 경기도, 용인시 |
| 23 | 이름 (NAM, 거주자 · 입주 기관) | 국제캠퍼스 |
| 25 | 건물 | 공학관 |
| 27 | 층 | 3 |
| 28 | 호실 | 301 |
| 22 | 추가 위치 정보 (LOC) | 중앙도서관 열람실 |

23 은 원래 입주 기관 이름 칸입니다. 캠퍼스 이름을 여기에 넣을지는 기관이 규칙으로 정합니다(Cisco 는 「venue name」이라고 부릅니다). 장비마다 채울 수 있는 칸이 다릅니다.

## 장비별 지원 현황

| 제품 | RFC 5580 | 대신 쓸 수 있는 것 |
|---|---|---|
| Cisco Catalyst 9800 (IOS XE) | 17.9.1 부터, 802.1X 사용자만. 국가 · 시도 · 시 · venue name · 우편번호, 좌표. Location-Capable 은 17.11.1 부터 | Called-Station-Id 형식 선택 |
| HPE Aruba (AOS-10) | Operator-Name · Location-Information 지원 (제조사 문서) | 제조사 속성(VSA) Aruba-Location-Id (AP 이름), Aruba-AP-Group |
| 그 밖의 무선 컨트롤러, 인증 어플라이언스 | 제조사 확인 필요 | 제조사 속성, AP 이름 |

FreeRADIUS 는 RFC 5580 사전(dictionary)을 기본으로 갖고 있습니다. Location-Data 는 바이트열로 들어오므로 로그를 남긴 뒤 풀어서 봅니다. 국산 인증 서버가 이 속성을 저장하고 조회할 수 있는지는 제조사에 물어야 합니다.

참고: Cisco 「Configuring RFC 5580 Location Attributes」(Catalyst 9800 설정 안내서, IOS XE 17.9.1) · [HPE Aruba AOS-10, Location-Information for Access and Accounting Requests](https://arubanetworking.hpe.com/techdocs/central/latest/content/aos10x/cfg/aps/gen-rad-lis.htm)

## 인증 실패 기록과 자료 연계

| 자료 | 언제 생기나 | 알 수 있는 것 |
|---|---|---|
| RADIUS 인증 요청 | 성공 · 실패 모두 | 사용자 (우리 기관이면 실제 ID), 단말 MAC, AP, 위치 |
| RADIUS 세션 기록 (accounting) | 성공한 세션만 | 시작 · 끝 시각, 받은 IP (Framed-IP-Address), 사용량 |
| CUI | 성공만 | 익명 ID 뒤의 같은 사용자 |
| 무선 컨트롤러 API · syslog | 연결된 단말 | AP 이름 · 그룹, 단말 IP, 신호 세기, AP 사이 이동 |
| DHCP 로그 | IP 를 받은 단말 | MAC 과 IP, 시각 |

실패는 인증 서버의 요청 기록에만 남습니다. 그래서 인증 서버 로그에 AP 와 위치를 함께 남겨야 합니다. 자료끼리는 단말 MAC 과 시각으로 잇습니다. 무작위 MAC 이라도 한 세션 안에서는 같습니다.

## 오픈소스 연계 구성 예

```text
[상용 · 이미 있음]    AP · 무선 컨트롤러 ── API · syslog ─────────────────┐
                         │ 인증 요청 · 세션 기록                          │
                         ▼                                                │
[상용 · 이미 있음]    내장 RADIUS · 인증 어플라이언스                     │
                         │                                                │
                         ▼                                                │
[오픈소스 · 보충]     FreeRADIUS 중계 ──▶ NRO · RO (위치 지움)            │
                         │ JSON 로그                                      │
                         ▼                                                │
[오픈소스]            수집 Vector · promtail ◀────────────────────────────┘
                         ▼
[오픈소스]            저장 Loki · ClickHouse ──▶ 조회 Grafana
```
{: .diagram}

| 구성 요소 | 구분 | 맡는 일 |
|---|---|---|
| 무선 컨트롤러 · AP | 상용 | 단말 접속, RFC 5580 위치(지원하면), API · syslog 로 단말 · AP 정보 |
| 내장 RADIUS · 인증 어플라이언스 | 상용 | 계정 확인, 성공 · 실패 판정 |
| FreeRADIUS 중계 | 오픈소스 (보충) | 캠퍼스 판별과 Operator-Name 부여, 방문자 CUI 요청, 인증 요청 · 세션 기록 JSON 로그, NRO 로 넘길 때 위치 지움 |
| Vector · promtail | 오픈소스 | 로그와 컨트롤러 API 자료 수집 |
| Loki · ClickHouse | 오픈소스 | 원본 로그 보관(Loki), 시간 · 캠퍼스별 집계(ClickHouse) |
| Grafana | 오픈소스 | 캠퍼스별 성공 · 실패 화면, 사용자 · MAC · CUI 로 찾기 |

상용 장비와 인증 판정은 그대로 두고, 장비가 못 하는 기록과 표시만 오픈소스로 덧붙입니다. Cisco 9800 은 RESTCONF · NETCONF(YANG 모델), Aruba 는 REST API 로 단말과 AP 정보를 내줍니다.

### FreeRADIUS 중계 서버 추가 구성

```text
뒤에 두기:  AP · 무선 컨트롤러 (내장 RADIUS) ──▶ FreeRADIUS 중계 ──▶ NRO · RO
앞에 두기:  AP · 무선 컨트롤러 ──▶ FreeRADIUS 중계 ──▶ 어플라이언스 인증 서버 · NRO · RO
```
{: .diagram}

- 상용 장비는 그대로 두고 RADIUS 서버 주소만 중계로 돌립니다. 뒤에 두면 로밍 요청만, 앞에 두면 모든 요청이 지납니다.
- 요청을 보낸 장비(NAS-IP-Address · NAS-Identifier)나 AP MAC 으로 캠퍼스를 판별해 값을 붙이고 로그에 남깁니다.
- 인증 요청과 세션 기록이 모두 지나므로 캠퍼스별 성공 · 실패 · 사용량을 한곳에서 봅니다.
- Operator-Name 도 여기서 넣습니다. NRO 도 대학 RO 를 거쳐 온 요청에 같은 방식(요청을 보낸 장비로 판별)으로 Operator-Name 을 붙입니다.

## 기관 조치 사항

1. 이름 규칙부터 정합니다. 캠퍼스(23), 건물(25), 층(27)을 쓰고, 건물 이름은 시설 대장의 건물 코드를 그대로 씁니다.
2. 무선 컨트롤러의 AP 그룹과 사이트를 캠퍼스 · 건물 단위로 맞춥니다. RFC 5580 을 못 보내는 장비도 이것으로 대응표를 만듭니다.
3. RFC 5580 을 지원하면 켜고, 지원하지 않으면 FreeRADIUS 중계에서 캠퍼스 값을 붙여 로그에 남깁니다.
4. 밖으로 보낼 필요가 없는 위치 속성은 FreeRADIUS 중계에서 지웁니다.

## F-Ticks 통계 형식

F-Ticks 는 인증 한 건을 syslog 한 줄로 남기는 연합 통계 형식입니다.

```text
F-TICKS/eduroam/1.0#REALM=example.ac.kr#VISCOUNTRY=KR#VISINST=1visit.example.ac.kr#CSI=…#RESULT=OK#
```
{: .diagram}

유럽은 참여기관 RADIUS 서버와 NRO 가 형식을 맞춰 중앙으로 보냅니다. 나라를 넘는 로밍을 파악하려는 것이 큰 목적입니다. 웹 ID 연계(SAML)에도 같은 형식이 있어 Shibboleth IdP 와 SimpleSAMLphp 가 지원하고, eduGAIN 이 시범 수집을 합니다. GÉANT 의 F-Ticks 수집은 유럽 회원 전용입니다.

참고: [F-Ticks howto](https://monitor.eduroam.org/f-ticks/howto.php) · [f-ticks.edugain.org](https://f-ticks.edugain.org/about.html) · draft-johansson-fticks (2013, 만료된 인터넷 초안)
