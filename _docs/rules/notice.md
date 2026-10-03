---
title: 고지문 게시 규정
section: 규정
section_url: /rules/
updated: 2026-10-03
version: v1.0
---

대한민국 eduroam 의 이름으로 서비스를 제공하거나 운영하는 모든 기관은, 자기 eduroam 안내 페이지에 **참여 선언**과 **공통 고지문** 두 가지를 게시해야 합니다.

## 왜 게시하나

eduroam 과 eduroam 로고는 GÉANT Association 의 등록상표이고, 국내 상표권은 한국과학기술정보연구원(KISTI)이 보유합니다(상표등록 제40-1190084호). 이름을 쓰는 쪽이 누구의 권리로 무엇을 하고 있는지 밝히는 것이 상표를 쓰는 조건입니다.

eduroam Compliance Statement 4.8 도 로밍 운영기관이 참여기관 정보와 지원 연락처를 웹에 공개하도록 정합니다. 참여기관 쪽 게시가 그 정보의 출발점입니다.

## 누가 무엇을 싣나

역할에 따라 **선언 문장만** 달라집니다. 공통 고지문은 세 역할 모두 원문 그대로입니다.

| 역할 | 선언 문장 | 공통 고지문 |
|---|---|---|
| 국가 로밍 운영기관(NRO) | 불필요 — 고지문 첫 문장이 겸합니다 | 기준 사본을 게시 |
| 대학 로밍 운영기관(RO) | `○○는 대한민국 에듀롬 대학 로밍 운영기관(RO)으로서 참여 대학의 에듀롬 로밍을 운영합니다.` | 원문 그대로 |
| 참여기관 (IdP+SP, SP-only) | `○○는 대한민국 에듀롬 참여기관으로서 소속 구성원과 방문자에게 국내외 에듀롬 무선 인증 서비스를 제공합니다.` | 원문 그대로 |

기관명 뒤 조사는 받침에 맞게 고칩니다 — `예제연구소는`, `한국과학기술정보연구원은`.

## 게시 요건

- 글자 크기 **14px(0.875rem) 이상**
- 배경 대비 **4.5:1 이상**(WCAG 2.1 AA), 링크는 밑줄을 유지합니다
- 숨기지 않습니다 — `display:none`, `visibility:hidden`, `opacity` 1 미만, 화면 밖 배치, `aria-hidden`, 접기·모달 안에 넣기 모두 해당합니다
- **HTML 텍스트**로 둡니다. 이미지로 만들거나 JavaScript 로 나중에 넣지 않습니다
- 링크에 `rel="nofollow"`, `rel="sponsored"`, `rel="ugc"` 를 붙이지 않습니다
- 문장, 링크 주소, 링크 순서, 첫 줄 제목을 바꾸지 않습니다. 요약·번역·줄바꿈 편집도 하지 않습니다

## 기준 사본

공통 고지문의 **기준 사본은 [eduroam.kreonet.net](https://eduroam.kreonet.net) 바닥글**입니다. 아래 블록은 그 사본을 그대로 옮긴 것입니다. 문구를 고쳐야 하면 각자 고치지 말고 NRO 에 요청합니다.

### 국문

```html
<!-- BEGIN eduroam-KR notice: NRO 가 정한 공통 문구입니다. 한 글자도 바꾸지 마세요. (CLAUDE.md 참고) -->
<p class="edurkr-notice"><strong>대한민국 eduroam 서비스 및 상표 고지</strong><br>한국과학기술정보연구원(KISTI)의 국가과학기술연구망(KREONET)은 대한민국 에듀롬 국가 로밍 운영기관(NRO)으로서 대학 로밍 운영기관(RO)인 한국교육정보화재단(KREN)과 협력하여 국내외 에듀롬 무선 인증 서비스를 제공합니다. 대한민국 에듀롬 서비스는 2012년 글로벌 에듀롬 운영기관인 GÉANT와의 협약에 따라 KREONET이 운영하며, 국내 에듀롬 상표에 관한 권리(<a href="https://doi.org/10.8080/4020150095410">상표등록 제40-1190084호</a>)는 KISTI가 보유하고 있습니다. 대한민국 에듀롬 공식 웹사이트는 <a href="https://eduroam.kreonet.net">eduroam.kreonet.net</a>입니다. 에듀롬(eduroam, education roaming)은 2003년 유럽 연구망 연합 TERENA(현 GÉANT)에서 시작된 글로벌 Wi-Fi 인증 로밍 서비스로, eduroam 로고와 eduroam®은 <a href="https://eduroam.org/eduroam-trademark-information/">GÉANT Association의 등록상표</a>입니다.</p>
<!-- END eduroam-KR notice -->
```

### 영문

```html
<!-- BEGIN eduroam-KR notice (en): NRO 가 정한 공통 문구입니다. 한 글자도 바꾸지 마세요. (CLAUDE.md 참고) -->
<p class="edurkr-notice" lang="en"><strong>eduroam Korea service and trademark notice</strong><br>KREONET<span class="text-body-secondary">(Korea Research Environment Open NETwork)</span>, the national research network of KISTI<span class="text-body-secondary">(Korea Institute of Science and Technology Information)</span>, is the NRO<span class="text-body-secondary">(National Roaming Operator)</span> for eduroam in the Republic of Korea and, in cooperation with the university RO<span class="text-body-secondary">(Roaming Operator)</span> - KREN<span class="text-body-secondary">(KoRea Education Network)</span>, provides domestic and international eduroam wireless authentication services. The eduroam service in Korea is operated by KREONET under a 2012 agreement with GÉANT, the operator of global eduroam, and the eduroam trademark in Korea is registered to KISTI (<a href="https://doi.org/10.8080/4020150095410">Korean Trademark Reg. No. 40-1190084</a>). The official eduroam Korea website is <a href="https://eduroam.kreonet.net">eduroam.kreonet.net</a>. eduroam (education roaming) is a global Wi-Fi authentication roaming service that originated in 2003 within TERENA (now GÉANT), the association of European research networks, and the eduroam logo and eduroam® are <a href="https://eduroam.org/eduroam-trademark-information/">registered trademarks of the GÉANT Association</a>.</p>
<!-- END eduroam-KR notice (en) -->
```

[site-template](https://github.com/eduroam-kr/site-template) 을 쓰면 위 블록이 이미 들어 있습니다. 기관명 한 군데만 바꾸면 됩니다.

## 지키지 않으면

NRO 가 시정을 요구하고, 이행되지 않으면 해당 realm 의 로밍을 중단할 수 있습니다. 근거는 eduroam Compliance Statement 4.9 와 5.1 입니다 — 로밍 운영기관은 자기 나라의 IdP·SP 가 규칙을 지키도록 해야 하고, 지키지 않는 것이 확인되면 적절한 조치를 취하도록 되어 있습니다.
