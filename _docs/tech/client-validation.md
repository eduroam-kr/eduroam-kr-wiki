---
title: 운영체제별 서버 확인 설정
section: 기술
section_url: /tech/
updated: 2026-11-15
---

단말이 서버를 확인하지 않으면 eduroam 이라는 같은 이름을 띄운 가짜 AP 에도 비밀번호(또는 그 해시)를 넘깁니다. 이 문서는 사용자가 고를 수 있는 설정 방식, 운영체제마다 다른 화면과 동작, 기관 담당자가 준비할 것을 정리합니다. 인증서 자체는 [RADIUS 서버 인증서와 CA]({{ '/tech/eap-certificates/' | relative_url }})에서 다룹니다.

## 서버 확인 방식

| 방식 | 단말이 믿는 것 | 평가 |
|---|---|---|
| ① 확인 안 함 (CA 를 지정하지 않음) | 아무것도 확인하지 않음 | 가짜 AP 에 그대로 접속 |
| ② TOFU (Trust On First Use, 처음 받은 서버 인증서를 믿음) | 첫 접속 때 받은 서버 인증서 | 첫 접속이 가짜면 가짜를 믿음. 인증서가 바뀌면 끊김 |
| ③ 기관이 공개한 CA 를 지정 | 지정한 CA 와 서버 이름 | 권장 |
| ④ 기관이 배포한 설정 프로그램 · 프로파일 사용 | ③ 과 같음 | 권장. 사용자 실수가 없음 |

단말이 묻는 것은 「지금 비밀번호를 받으려는 이 서버가 정말 우리 기관 서버입니까?」입니다. 에듀롬 SSID 는 누구나 만들 수 있습니다. 가짜 AP 는 라즈베리파이나 노트북 한 대로 만들 수 있고, 학회장 · 세미나실 · 공항 · 카페처럼 사람이 많은 곳이 표적입니다.

## 권장 설정 항목

| 칸 | 넣을 값 | 하지 말 것 |
|---|---|---|
| CA 인증서 | 기관이 안내한 CA | 「Do not validate」, 「지정되지 않음」 |
| 도메인 (서버 이름) | 기관이 안내한 값, 예: `radius.example.ac.kr` | 비워 두기. 같은 CA 가 발급한 남의 서버도 통과한다 |
| 익명 ID | `anonymous@example.ac.kr`, 잘 모르면 비움 | 다른 기관 도메인 |

익명 ID 를 비우면 실제 ID 가 바깥에도 그대로 실립니다. 요청이 가는 길은 어느 쪽이든 맞고, ID 를 감추느냐 드러내느냐의 차이입니다. 자세한 내용은 [익명 ID 와 realm]({{ '/tech/identity-and-realm/' | relative_url }})에 있습니다.

## 운영체제별 현황

| 운영체제 | 고를 수 있는 가장 느슨한 선택 | 그때 단말이 믿는 것 |
|---|---|---|
| 안드로이드 순정 13 이상 | 처음 사용 시 신뢰 (TOFU, 지원 단말만) | 루트 CA 또는 서버 인증서 |
| 안드로이드 순정 11 QPR1 ~ 12 | 없음. CA 지정이 강제됨 | 지정한 CA |
| 안드로이드 일부 제조사 단말 | 「검증 안 함」이 남아 있는 경우 | 아무것도 확인하지 않음 |
| iOS · macOS | 「신뢰」 (TOFU) | 서버 인증서 |
| Windows 11 | 확인 창에서 「연결」 (TOFU) | 서버 인증서 지문 |
| 리눅스 (NetworkManager) | 「CA 인증서 필요 없음」 | 아무것도 확인하지 않음 |

같은 안내문 한 장으로는 모든 단말을 다루지 못합니다. 사용자가 보는 선택지는 운영체제와 버전, 제조사에 따라 다릅니다.

## 안드로이드

### 「검증 안 함」 선택지 폐지 경과

| 시기 | 변화 |
|---|---|
| Android 11 초기까지 | CA 인증서에 「Do not validate」가 있어 서버를 확인하지 않고 접속 |
| Android 11 QPR1 (2020. 12.) | 설정 화면에서 「Do not validate」 삭제 |
| Android 12 | 엔터프라이즈 네트워크의 서버 인증서 확인 의무화 |
| Android 13 | 「처음 사용 시 신뢰」(TOFU) 추가, 지원 단말에만 표시 |

Android 11 QPR1 부터 12 까지는 TOFU 도 없어서, CA 를 지정하거나 「시스템 인증서 사용」과 도메인을 함께 넣어야 저장됩니다. 「검증 안 함」을 안내하던 기관은 이 시기에 민원이 몰렸습니다. 2020년 말 영국 Jisc 와 캐나다 CANARIE 는 「검증 안 함」 안내문을 고치라고 참여기관에 공지했습니다. 안드로이드 11 이상의 CA 사전 설치는 [CA 인증서 제공 및 사전 설치]({{ '/tech/ca-install/' | relative_url }})도 참고합니다.

### 현행 설정 화면과 TOFU 동작

- CA 인증서 선택지(AOSP 기준)는 시스템 인증서 사용, 처음 사용 시 신뢰(지원 단말), 인증서 설치, 설치한 CA 입니다.
- CA 를 고르지 않거나, 인증서를 골라 놓고 도메인을 비우면 저장 버튼이 눌리지 않습니다.
- TOFU 로 「연결」을 누르면 서버가 보낸 체인을 보고 저장할 것을 정합니다.
    - 자기 서명 루트까지 보내면 그 루트 CA 를 저장하므로, 서버 인증서를 갱신해도 그대로 접속됩니다.
    - 중간 CA 까지만, 또는 서버 인증서만 보내면 서버 인증서 해시를 저장하므로, 갱신하면 끊깁니다.
    - 도메인 칸은 서버 인증서의 SAN(없으면 CN)으로 자동 입력됩니다.

RADIUS 서버가 체인에 루트 CA 까지 실어 보내면 안드로이드 TOFU 사용자는 서버 인증서 갱신 때 끊기지 않습니다. 이 동작은 안드로이드에만 해당합니다.

### 제조사별 차이

AOSP 에는 제조사가 바꿀 수 있는 설정 값 `config_wifiAllowInsecureEnterpriseConfigurationsForSettingsAndSUW` 가 있습니다. 기본값은 false 이고 주석은 false 를 강력히 권장합니다. 제조사가 이 값을 켜고 설정 화면을 고치면 「검증 안 함」이 다시 보입니다. 산업용 단말 제조사 Datalogic 은 이 선택지를 되살리는 방법을 공식 포럼에 안내했고, 삼성 단말에서 다시 생겼다는 사용자 보고도 해외 메일링 리스트에 있습니다(공식 자료는 없음). TOFU 는 제조사가 supplicant HAL 을 구현해야 나타나므로, 같은 Android 13 이라도 단말마다 선택지가 다릅니다.

안내 페이지에는 선택지 이름을 그대로 적어 줍니다. 「Do not validate」 · 「Don't validate」 · 「지정되지 않음」은 고르지 않도록 하고, 「처음 사용 시 신뢰」보다 CA 지정을 먼저 안내합니다.

참고: [Android Wi-Fi TOFU](https://source.android.com/docs/core/connect/wifi-tofu) · [Pixel 도움말](https://support.google.com/pixelphone/answer/2844832) · [Jisc 공지](https://community.jisc.ac.uk/node/48946) · [CANARIE 공지](https://www.canarie.ca/document/caf-eduroam-android-users-will-no-longer-connect-requires-a-cat-profile-change/) · [Datalogic 포럼](https://discussion.datalogic.com/t/how-to-restore-do-not-validate-option-for-ca-certificate-choice-in-wpa-wpa2-wpa3-enterprise-wi-fi-security-profile-on-android-11/2622) · [GÉANT cat-users (2021-05)](https://lists.geant.org/sympa/arc/cat-users/2021-05/msg00015.html) · AOSP packages/modules/Wifi (InsecureEapNetworkHandler.java, WifiConfigManager.java, ServiceWifiResources/res/values/config.xml)

## iOS · macOS

- 수동 연결에는 「검증 안 함」이 처음부터 없습니다. 첫 접속 때 서버 인증서를 보여 주고 「신뢰」를 묻습니다.
- 「신뢰」를 누르면 SSID 와 서버 인증서의 SHA-1 해시를 묶어 저장합니다. 체인을 따라 올라가 CA 를 기억하지는 않습니다.
- 서버 인증서가 바뀌면 해시가 달라져 다시 묻거나 접속에 실패합니다.
- 구성 프로파일에 신뢰할 CA 나 서버 이름(`TLSTrustedServerNames`)이 있으면 「신뢰」 예외 자체를 허용하지 않습니다.

CA 와 서버 이름을 넣은 구성 프로파일(.mobileconfig)을 배포하면 TOFU 경로가 사라집니다.

참고: Apple 공개 소스 [eap8021x](https://github.com/apple-oss-distributions/eap8021x) (EAPTLSUtil.c, EAPTLSTrustExceptionUtil.c)

## Windows

Windows 11 은 다음 중 하나일 때만 서버를 믿습니다.

- 프로파일에 서버 인증서 지문이 있음. 확인 창에서 「연결」을 누르면 자동으로 들어갑니다(TOFU).
- 루트 CA 가 신뢰 저장소에 있고, 그 루트의 지문이 프로파일에 있고, 서버 이름이 맞음.

공인 CA 라도 프로파일에 루트 지문이 없으면 확인 창이 뜹니다. Windows 10 은 EAP 방식마다 동작이 달랐고, 루트 CA 가 신뢰 저장소에 있기만 해도 통과한 경우가 있었습니다. Windows 10 에서 되던 프로파일이 11 로 올린 뒤 실패하면 프로파일에 루트 지문을 넣습니다.

참고: [EAP: What's changed in Windows 11](https://learn.microsoft.com/en-us/windows-server/networking/technologies/extensible-authentication-protocol/windows-11-changes)

## 리눅스

- NetworkManager(GNOME · KDE 설정 화면)에 「CA 인증서 필요 없음」 체크가 남아 있습니다. 켜면 서버를 전혀 확인하지 않습니다.
- TOFU 가 없어 기억하는 것도 없습니다. 끊기지는 않지만 가장 위험한 설정입니다.
- 바른 설정은 CA 인증서 파일과 도메인(wpa_supplicant 의 `domain_suffix_match`)입니다.
- wpa_supplicant 를 직접 설정하면 서버 인증서를 고정할 수도 있습니다. `ca_cert="hash://server/sha256/<해시>"` 이며, 서버 인증서가 바뀌면 끊깁니다.

## 접속 장애 시 조치

CA 를 지정하지 않고 첫 접속 때 「신뢰」를 누른 단말은 대개 CA 가 아니라 그때 받은 서버 인증서를 기억합니다. 서버 인증서는 유효기간이 짧고 인증 어플라이언스가 자동으로 갱신하기도 합니다. 인증서가 바뀌면 단말은 처음 보는 서버로 여겨 접속을 거부하거나 다시 묻습니다.

1. 가짜 AP 일 수 있으므로 기관 안내 페이지에서 CA 와 서버 인증서 변경 공지를 먼저 확인합니다.
2. 바뀌었다면 저장된 eduroam 프로파일을 지우고 다시 설정합니다. 이때 CA 를 지정하면 다음 갱신 때도 그대로 접속됩니다.

TOFU 를 쓴다면 첫 접속은 소속 기관 내부처럼 믿을 수 있는 곳에서 합니다. 기관이 무선 침입 방지 시스템으로 가짜 AP 를 막고 있어야 의미가 있고, 그래도 CA 지정보다 약합니다.

## 기관 담당자 준비 사항

- 안내 페이지를 운영체제별로 나눕니다. 안드로이드는 버전과 제조사에 따라 선택지 이름이 다르다는 점도 적습니다.
- 맨 위에는 공통 값 세 가지를 둡니다. CA 인증서 파일, SHA-256 지문, 서버 이름입니다.
- 가능하면 프로파일로 배포합니다. Apple 은 .mobileconfig, Windows 와 안드로이드는 설정 프로그램(geteduroam 등)을 씁니다.
- 신규 사용자 안내문, 계정 발급 메일, 헬프데스크 응대에도 같은 내용을 넣습니다.
- 서버 인증서를 바꾸기 전에 공지합니다. TOFU 로 접속한 단말(iOS · macOS · Windows 11 · 일부 안드로이드)은 끊깁니다.
- 「검증 안 함」을 안내하는 옛 문서와 게시물을 찾아 내립니다.

## 가짜 AP 탐지와 차단

eduroam SSID 는 누구나 띄울 수 있으므로, 캠퍼스 안의 가짜 AP 는 ID 와 비밀번호를 노리는 중간자 공격의 출발점이 됩니다. 기관 Wi-Fi 를 운영한다면 WIPS(Wireless Intrusion Prevention System, 무선 침입 방지 시스템)로 다음을 합니다.

- 우리 기관 AP 가 아닌 eduroam SSID 를 탐지합니다.
- 그 AP 의 위치를 추적합니다.
- 접속을 차단하고 현장에서 제거합니다.

사용자 단말의 CA 확인이 첫째 방어선이고 WIPS 가 둘째 방어선입니다. 어느 하나만으로는 부족합니다.
