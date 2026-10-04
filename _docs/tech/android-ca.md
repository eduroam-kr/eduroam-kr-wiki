---
title: 안드로이드 11 이상의 CA 인증서
section: 기술
section_url: /tech/
updated: 2026-10-04
toc: false
---

안드로이드 11 부터 Wi-Fi 설정의 CA 인증서 **"사용 안 함"** 선택지가 없어졌습니다. WPA2 엔터프라이즈로 접속하려면 CA 인증서를 미리 설치하거나 **"시스템 인증서 사용"** 을 골라야 합니다.

이것은 안드로이드가 막아 둔 것이 아니라 원래 지켜야 할 절차입니다. CA 인증서를 확인하지 않으면 같은 SSID 를 띄운 가짜 접속점에 아이디와 비밀번호를 그대로 넘겨 주게 됩니다. [운영약관]({{ '/rules/provisions/' | relative_url }}) 도 참여기관이 자기 인증서버의 CA 인증서와 서버 이름을 이용자에게 알리도록 정합니다.

## 참여기관이 할 일

이용자가 설정할 때 필요한 값을 안내 페이지에 적어 두어야 합니다.

- CA 인증서 — 어느 CA 인지, 공인 CA 면 "시스템 인증서 사용" 으로 충분한지
- 도메인 / 서버 인증서 이름 — 이용자가 입력란에 그대로 넣을 값
- EAP 방식과 2단계 인증 (예: PEAP + MSCHAPv2)
- 익명 아이디(outer identity) 를 쓰는지

## 참고

- [강원대학교 정보화본부 QnA (2023-11-06)](https://wwwk.kangwon.ac.kr/itservice/selectBbsNttView.do?bbsNo=115&nttNo=164915)
