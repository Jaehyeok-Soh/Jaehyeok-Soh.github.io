---
title: "Portfolio"
layout: single
permalink: /portfolio/
classes:
  - wide
  - portfolio-page
author_profile: false
toc: false
---

<div class="portfolio-hero">
  <span class="portfolio-hero__label">BACKEND × 3D GRAPHICS</span>

  <p class="portfolio-hero__lead">
    C#/.NET 백엔드 3년 실무와 C++/DirectX11 자체 엔진 구현 경험을 바탕으로,
    서버와 3D 클라이언트를 함께 다룹니다.
  </p>

  <p class="portfolio-hero__description">
    서버와 클라이언트의 통신 구조, 세션 및 상태 관리,
    3D 렌더링과 물리, 애니메이션을 직접 구현한 프로젝트를 정리했습니다.
  </p>
</div>

{% include portfolio-section.html
category="multiplayer"
eyebrow="MULTIPLAYER"
title="Server · Client Projects"
description="클라이언트와 서버를 함께 구현하며 세션 관리, 패킷 처리, 상태 동기화와 멀티플레이 구조를 학습한 프로젝트입니다."
placeholder="MULTIPLAYER"
%}

{% include portfolio-section.html
category="client"
eyebrow="CLIENT · ENGINE"
title="3D Client Projects"
description="C++과 DirectX 기반으로 3D 렌더링, 물리, 상태 머신, 애니메이션 툴을 구현한 프로젝트입니다."
placeholder="CLIENT"
%}

{% include portfolio-section.html
category="backend"
eyebrow="BACKEND CAREER"
title="Backend Experience"
description="C#/.NET 환경에서 상태 관리, 비동기 처리, 외부 시스템 연동과 서비스 운영을 경험한 업무 및 프로젝트입니다."
placeholder="BACKEND"
%}
