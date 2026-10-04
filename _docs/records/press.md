---
title: 에듀롬 KR 언론 보도
section: 기록
section_url: /records/
updated: 2026-10-04
toc: false
---

<table>
  <thead>
    <tr><th>구분</th><th>일자</th><th>발행처</th><th>제목</th><th>요약</th><th>비고</th></tr>
  </thead>
  <tbody>
  {%- for a in site.data.press %}
    <tr>
      <td>{{ a.kind }}</td>
      <td>{{ a.date }}</td>
      <td>{{ a.outlet }}</td>
      <td>{% if a.url %}<a href="{{ a.url }}">{{ a.title }}</a>{% else %}{{ a.title }}{% endif %}</td>
      <td>{{ a.summary }}</td>
      <td>{{ a.note }}</td>
    </tr>
  {%- endfor %}
  </tbody>
</table>
