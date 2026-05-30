---
title: "Architecture Decision Records"
layout: single
permalink: /adr/
author_profile: false
---

프로젝트별 주요 기술적 의사결정과 그 배경을 기록합니다.

{% assign projects = site.adrs | group_by: "project" | sort: "name" %}

{% for project in projects %}
## {{ project.name }}

{% assign sorted = project.items | sort: "date" | reverse %}

<table>
  <thead>
    <tr>
      <th>날짜</th>
      <th>제목</th>
      <th>상태</th>
    </tr>
  </thead>
  <tbody>
  {% for adr in sorted %}
    <tr>
      <td>{{ adr.date | date: "%Y-%m-%d" }}</td>
      <td><a href="{{ adr.url | relative_url }}">{{ adr.title }}</a></td>
      <td><code>{{ adr.status | default: "proposed" }}</code></td>
    </tr>
  {% endfor %}
  </tbody>
</table>

{% endfor %}