---
title: 에듀롬 KR 발간 문서
section: 기록
section_url: /records/
updated: 2026-10-04
toc: false
---

옛 위키 `wiki.kreonet.net/eduroamkr` 에서 옮겼습니다. 문구는 그대로 둡니다.

목록은 [`_data/publications.yml`](https://github.com/eduroam-kr/eduroam-kr-wiki/blob/main/_data/publications.yml) 이 정본입니다. 빠진 문서가 있으면 그 파일에 Pull Request 를 보내 주세요.

{% assign groups = "회원기관,운영기관" | split: "," %}
{%- for g in groups %}
{%- assign rows = site.data.publications | where: "by", g %}

## 에듀롬 KR {{ g }} 발간 문서

{% if rows.size == 0 %}아직 없습니다.{% else %}
<table>
  <thead>
    <tr><th>발간일</th><th>제목</th><th>주요 내용</th><th>저자</th><th>권/호</th><th>학회, 출판사</th><th>비고</th></tr>
  </thead>
  <tbody>
  {%- for d in rows %}
    <tr>
      <td>{{ d.date }}</td>
      <td>{% if d.url %}<a href="{{ d.url }}">{{ d.title }}</a>{% else %}{{ d.title }}{% endif %}</td>
      <td>{{ d.summary }}</td>
      <td>{{ d.authors }}</td>
      <td>{% if d.volume_url %}<a href="{{ d.volume_url }}">{{ d.volume }}</a>{% else %}{{ d.volume }}{% endif %}</td>
      <td>{{ d.publisher }}</td>
      <td>{{ d.note }}</td>
    </tr>
  {%- endfor %}
  </tbody>
</table>
{% endif %}
{%- endfor %}
