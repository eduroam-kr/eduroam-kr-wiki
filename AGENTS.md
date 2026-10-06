# eduroam-kr-wiki 작업 규약

대한민국 eduroam 운영 위키. 무엇을 하는지는 [README.md](README.md) 에 있고, 이 파일은 어떻게 작업할지만 다룬다.

## 공식 웹사이트와 겹치지 않는다

규범은 공식 웹사이트(`eduroam-kr-nro-web`), 해설은 여기, 구현은 `nro-docker` 다. 같은 주제를 셋에 써도 층이 다르면 중복이 아니다. 다만 **같은 층을 두 곳에 쓰지 않는다** — 규칙을 여기에 또 적으면 둘이 갈라진다.

조항 번호가 다리다. 공식 웹사이트가 요약과 번호를 두고, 여기가 전문과 근거를 둔다.

## 고지문은 복사해 오지 않는다

`rules/notice.md` 의 복사용 블록은 `onepage-html-site-theme` 의 원문과 글자까지 같아야 한다. 손으로 옮기지 말고 아래로 뽑는다.

```sh
sed -n '/BEGIN eduroam-KR notice:/,/END eduroam-KR notice -->/p' <onepage-html-site-theme 체크아웃>/index.html
```

고지문 자체를 고쳐야 하면 `eduroam-kr-nro-web`·`onepage-html-site-theme`·참여기관 포크를 같이 고치고 여기 블록도 다시 뽑는다.

## 되풀이되는 목록은 _data 에 둔다

언론 보도와 발간 문서처럼 같은 꼴이 쌓이는 것은 마크다운 표로 적지 않는다. `_data/press.yml`, `_data/publications.yml` 이 정본이고 쪽은 그걸 돌려 표를 찍는다. 파이프 하나 어긋나 표가 깨질 일이 없고, 항목 하나 더하는 Pull Request 가 쉬워진다.

산문은 그대로 마크다운에 둔다. 회의록이나 답변서를 YAML 로 쪼개면 원문이 아니라 내가 만든 구조가 된다.

## 국문만 쓴다

위키는 국내 운영 문서라 한국어 하나로 쓴다. GeGC 나 해외 NRO 가 읽어야 하는 문서는 그 문서 안에 영문 절을 둔다. `/en/` 두 벌을 만들지 않는다.

## 공개 저장소다

운영 설정을 올릴 때 실제 IP·호스트명·공유키를 넣지 않는다. 주소는 RFC 5737 문서용 대역, realm 은 `example.ac.kr` 을 쓴다. 실제 구성은 비공개 저장소에 둔다.

기관을 지목해 잘못을 적지 않는다. 패턴으로 적고, 그 기관에는 따로 연락한다.

## 껍데기는 테마가 맡는다

레이아웃·머리글·바닥글·CSS·테마 스크립트는 [multipage-jekyll-site-theme](https://github.com/eduroam-kr/multipage-jekyll-site-theme) 이 들고 있다. `_config.yml` 의 `remote_theme` 이 태그로 고정한다. 여기에 같은 경로의 파일을 두면 그것만 덮어쓴다 — 덮어쓰기 전에 테마 쪽에서 고칠 일인지 먼저 따진다.

메뉴는 `_data/nav.yml` 이 정한다. 문서 쪽 front matter 에서 `toc`, `print`, `print_toc` 로 목차와 인쇄를 켠다. 기본값은 `_config.yml` 의 `defaults` 에 있다.

색은 Bootstrap 5.3 기본 팔레트를 그대로 쓴다. 제목은 `<h1>`, `<h2>`, `<h3>` 를 그대로 쓰고 크기는 테마가 정한다 — 크기 때문에 다른 태그나 클래스를 쓰지 않는다.

front matter 의 `description` 은 쓰지 않는다. 본문 앞부분을 잘라 쓴다.

## 연결어미 뒤 쉼표는 지우지 않는다

`-고,` `-며,` `-면서,` 뒤의 쉼표는 한국어에서 가독성을 올린다. AI 글 판별 규칙(im-not-ai 의 C-11)이 이걸 "쉼표 과다"로 잡지만 여기서는 따르지 않는다. 절이 긴 기술 문장에서 쉼표를 빼면 어디서 끊어 읽을지 보이지 않는다.

윤문 도구를 돌릴 때 C-11 은 끈다.

## 커밋

```text
<type>: <무엇을 왜 했는지>
```

`feat` 새 문서 · `fix` 고침 · `docs` 문구 · `chore` 설정·도구.

- 제목에는 무엇을 왜 했는지 쓴다. "파일을 바꿈" 은 제목이 못 된다.
- `Claude-Session` 은 기재하지 않는다 (public 저장소).

## 로컬

```sh
bundle install
bundle exec jekyll build      # 경고가 하나도 없어야 한다
```

`_site/` 는 산출물이라 커밋하지 않는다.

## 문서

하드 랩 금지 — 문단·목록 항목을 각각 한 줄로 쓰고 줄바꿈은 렌더러에 맡긴다.
