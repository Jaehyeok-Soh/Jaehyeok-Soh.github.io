<%*
const category = await tp.system.suggester(
["Multiplayer", "Client / Engine", "Backend"],
["multiplayer", "client", "backend"],
false,
"포트폴리오 카테고리 선택"
);
const categoryLabel =
category === "multiplayer"
? "Multiplayer"
: category === "client"
? "Client"
: "Backend";

// YAML 프론트매터는 들여쓰기 공백이 있으면 깨지므로 반드시 좌측에 붙여서 출력해야 합니다.
tR += `---
title: "${tp.file.title}"
excerpt: "한 줄 설명"
header:
  teaser: /assets/images/
categories:
  - Portfolio
  - ${categoryLabel}
portfolio_category: "${category}"
tags:
  - Portfolio
  - ${categoryLabel}
toc: true
toc_label: "목차"
toc_sticky: true
---
`;
-%>
## 📝 프로젝트 개요

| 항목 | 내용 |
| :--- | :--- |
| **기간** | 0000.00 ~ 0000.00 |
| **인원** | 0인 |
| **역할** |  |
| **언어** |  |
| **기술** |  |

> 프로젝트가 무엇이고, 내가 무엇을 담당했는지 2~3문장으로 요약합니다.

## 기획 의도

* 프로젝트를 만든 이유
* 해결하거나 검증하려던 목표
* 기존 프로젝트를 활용했다면 기존 영역과 추가 구현 영역

## 담당 역할 및 기여

* 직접 설계한 부분
* 직접 구현한 부분
* 팀 공통 영역
* 다른 팀원이 구현한 부분
## 구현 내용

### 핵심 기능 1

* 목적
* 설계
* 구현 방식
* 결과

### 핵심 기능 2

* 목적
* 설계
* 구현 방식
* 결과
## 트러블슈팅
### 문제
> 실제로 발생한 증상
- 
### 원인
* 어떻게 원인을 확인했는지
* 실제 원인이 무엇이었는지
### 해결
* 어떤 방식으로 수정했는지
* 왜 그 방식을 선택했는지
* 수정 후 어떻게 검증했는지
## 결과 및 배운 점
* 완성한 기능
* 기술적으로 배운 점
* 현재 한계
* 다시 구현한다면 개선할 부분

## 관련 링크

* 영상:
* GitHub:
* 실행 페이지:
* 다운로드: