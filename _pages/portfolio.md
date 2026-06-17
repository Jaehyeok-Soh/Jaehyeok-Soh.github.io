---
title: "Portfolio"
layout: single
permalink: /portfolio/
classes: wide
---

# Portfolio

C#/.NET 백엔드 개발 경력과 C++/DirectX 게임 프로젝트 경험을 바탕으로  
실시간 멀티플레이 게임 서버 개발자로 전환을 준비하고 있습니다.

---

## Multiplayer / Server-Client

클라이언트와 서버를 함께 구현하며 멀티플레이 구조를 학습한 프로젝트입니다.

<div class="grid__wrapper">
{% assign multiplayer_projects = site.portfolio | where: "portfolio_category", "multiplayer" | sort: "order" %}
{% for post in multiplayer_projects %}
  {% include archive-single.html type="grid" %}
{% endfor %}
</div>

---

## Client / Engine

게임 클라이언트와 엔진 구조를 이해하기 위해 진행한 프로젝트입니다.  
서버 지원에서는 게임 객체, 상태 머신, 전투/콘텐츠 흐름을 이해한 보조 경험으로 활용합니다.

<div class="grid__wrapper">
{% assign client_projects = site.portfolio | where: "portfolio_category", "client" | sort: "order" %}
{% for post in client_projects %}
  {% include archive-single.html type="grid" %}
{% endfor %}
</div>

---

## Backend Career

C#/.NET 백엔드 개발 경험은 게임 서버의 세션 관리, 비동기 처리, 상태 관리, 운영 안정성 관점에서 연결하고 있습니다.

<div class="grid__wrapper">
{% assign backend_projects = site.portfolio | where: "portfolio_category", "backend" | sort: "order" %}
{% for post in backend_projects %}
  {% include archive-single.html type="grid" %}
{% endfor %}
</div>