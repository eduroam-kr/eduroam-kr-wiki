---
title: 2017.12. 에듀롬 KR 운영정책 세미나
section: 기록
section_url: /records/
created: 2017-12-19
toc: true
---

옛 위키 `wiki.kreonet.net/eduroamkr` 에서 옮겼습니다. 문구는 그대로 둡니다.

## 일시 및 장소

일자 : 2017-12-19

장소 : 전남대학교 공학 7호관

## 참석

- 조부승 (KISTI)
- 장민석 (KISTI)
- 최덕재 (전남대)
- 김성국 (전남대)
- 정재화 (전남대)

## 안건

- 13:00 - 14:00 KISTI NRO 운영현황 공유 및 에듀롬 국가정책 협의 (KISTI)
- 14:00 - 15:00 전남대 RO 운영현황 공유(전남대)
- 15:00 - 16:00 토론

## 회의록

KISTI(NRO) 에듀롬 대정부 공공영역 확장 논의사항 공유

- 공공영역 확장시 별도의 브랜드 네임 필요
- 정부 공개 AP 개수 확장
- 국민 인터넷 기본권 확보 (통신비 인하)

KISTI 내부 eduroam 과제 개발 방향 논의

전남대(RO) 통신3사 eduroam 연동 공동연구 착수사항 공유

- 통신3사 교육망에 참여하며 WiFi 개방하겠다 확답
- 현재 통신사 Public WiFi 광고 1분 시청 후 이용, but 수익 없음
- (통신3사 기조) 고속 LTE 서비스 유료 / Low QoS WiFi 서비스는 Public

NRO - RO 협력 체계 구축

NRO - RO간 RADIUS 라우팅 정책 논의

- 현재) Garbage Request가 Loop
  - KISTI (*.ac.kr) → CNU
  - CNU (not in list) → KISTI
  - KISTI 또는 CNU 에서 처리되지 못한 Request는 Loop
- 해결1) KISTI에 연동된 *.ac.kr은 CNU에도 연동
- 해결2) 잘못된 realm drop 위해 정규식 라우팅 활용 X

백업구간 논의

초중고 에듀롬 논의

- 캐나다 초중고 eduroam 활용 중, 미국 도입 검토
- 국내 도입은 학부모 반대로 무리 (낮은 연령 아이들 스마트기기 사용 관련)

국가 에듀롬 운영위원회 조직

- 18년 1월 첫 운영위원회 구성 모임 개최
- 운영위원회 : NRO, RO, 대표사용기관
- 실무협(WG)
  - KREONET(KISTI) eduroam 행사지원
  - KOREN, KREN도 eduroam 행사지원이 가능한지 문의할 예정
- 자문위원회 : 2차관, 교육망운영본부, NIA, ISP사업자
