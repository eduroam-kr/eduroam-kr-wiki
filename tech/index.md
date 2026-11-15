---
layout: default
title: 기술
permalink: /tech/
---
<div class="container with-toc py-5">
<div class="doc-body" markdown="1">

# 기술

realm 라우팅, 인증서, 로깅, 모니터링을 다룹니다. 실제로 돌아가는 구성은 [kr-nro-docker](https://github.com/eduroam-kr/kr-nro-docker) 에 있습니다.

### 이용자 안내

이용자용 안내는 공식 웹사이트에 있습니다. 이 위키의 기술 문서는 그 안내에 나오는 설정값의 의미와 기관 운영 방법을 설명합니다.

- [접속 방법](https://eduroam.kreonet.net/connect/): 접속 정보, CA 인증서 확인, 운영체제별 설정
- [접속이 안 될 때](https://eduroam.kreonet.net/connect/help/): 증상별 조치, 인증서 경고 대처, 문의 방법

### 인증서와 단말 설정

- [RADIUS 서버 인증서와 CA]({{ '/tech/eap-certificates/' | relative_url }})
- [운영체제별 서버 확인 설정]({{ '/tech/client-validation/' | relative_url }})

### 계정과 로밍

- [익명 ID 와 realm]({{ '/tech/identity-and-realm/' | relative_url }})
- [계정 · 비밀번호와 EAP 방식]({{ '/tech/credentials/' | relative_url }})
- [로밍 운영: 거절과 반복 실패 대응]({{ '/tech/roaming-operations/' | relative_url }})

### 운영과 기록

- [접속 장애 대응 (기관 담당자용)]({{ '/tech/troubleshooting/' | relative_url }})
- [다중캠퍼스 위치와 감사 기록]({{ '/tech/location-logging/' | relative_url }})

여기 문서는 [2026 대한민국 에듀롬 사용자 포럼](https://github.com/eduroam-kr/2026-user-forum-n3frast-slide) 발표 내용을 정리한 것입니다.

</div>
</div>
