# eduroam Korea 위키

대한민국 eduroam 의 **운영 규정, 기술 규격, 운영 기록**을 둡니다. <https://wiki.eduroam.kreonet.net>

이용자 안내와 가입 안내는 공식 웹사이트 [eduroam.kreonet.net](https://eduroam.kreonet.net) 에 있습니다. 여기와 거기의 역할이 다릅니다.

| | 무엇을 | 누가 읽나 |
|---|---|---|
| [공식 웹사이트](https://github.com/eduroam-kr/nro-site) | 지금 어떻게 쓰고, 어떻게 가입하고, 무엇을 지켜야 하나 | 이용자, 가입 희망 기관 |
| **이 위키** | 왜 그렇게 정했고, 무슨 일이 있었나 | 기관 담당자, 운영자 |
| [nro-docker](https://github.com/eduroam-kr/nro-docker) | 그 규격을 실제로 어떻게 구현하나 | 다른 NRO, 기관 엔지니어 |
| [eduroam-kr-db](https://github.com/eduroam-kr/eduroam-kr-db) | 기관 정보의 정본 | 기계, PR 보내는 담당자 |
| [site-template](https://github.com/eduroam-kr/site-template) | 참여기관 안내 페이지 템플릿 | 기관 웹 담당자 |

규범은 공식 웹사이트, 해설은 위키, 구현은 nro-docker 입니다. 같은 주제가 셋에 나와도 역할이 다르면 중복이 아닙니다.

## 구성

```
rules/      규정    고지문 게시, 운영약관, 참여기관 요건
tech/       기술    realm 라우팅, 인증서, 로깅, 모니터링
history/    연혁
records/    기록    사건 기록, 결정 기록
```

문서는 `_docs/<구역>/<이름>.md` 에 둡니다. 구역 첫 화면은 `<구역>/index.md` 입니다.

## 로컬에서 보기

```sh
bundle install
bundle exec jekyll serve
```

## 고치기

Pull Request 로 받습니다. 개정 이력은 git 이 남기므로 문서 안에 변경 이력 표를 따로 두지 않습니다. 날짜가 중요한 문서는 front matter 의 `updated` 와 `version` 을 올립니다.
