---
<%*
// 1. 기존 파일들 뒤져서 프로젝트 목록 추출
const projects = [...new Set(
  app.vault.getMarkdownFiles()
    .filter(f => f.path.startsWith("_adrs/"))
    .map(f => app.metadataCache.getFileCache(f)?.frontmatter?.project)
    .filter(Boolean)
)];

let selectedProject;
if (projects.length > 0) {
  projects.push("+ 새 프로젝트");
  const pick = await tp.system.suggester(projects, projects);
  selectedProject = (pick === "+ 새 프로젝트") ? await tp.system.prompt("새 프로젝트명(영문/대시추천)") : pick;
} else {
  selectedProject = await tp.system.prompt("프로젝트명(영문/대시추천)");
}

// 2. 상태 선택 스크립트
const statusOptions = ["accepted", "proposed", "deprecated", "superseded"];
const selectedStatus = await tp.system.suggester(statusOptions, statusOptions);

const statusColors = { 
  accepted: "green", 
  proposed: "orange", 
  deprecated: "red", 
  superseded: "gray" 
};
const statusColor = statusColors[selectedStatus] || "green";
const statusLabel = selectedStatus.charAt(0).toUpperCase() + selectedStatus.slice(1);
-%>
layout: post
title: "ADR: <% tp.file.cursor(1) %>"
excerpt: "<% tp.file.cursor(2) %>"
project: "<% selectedProject %>"
status: "<% selectedStatus %>"
tags:
  - ADR
toc: true
toc_label: "목차"
toc_sticky: true
---

## 📌 개요

| 항목 | 내용 |
| :--- | :--- |
| **프로젝트** | <% selectedProject %> |
| **날짜** | <% tp.date.now("YYYY-MM-DD") %> |
| **상태** | <span style="color:<% statusColor %>">**<% statusLabel %>**</span> |

---

## 🔍 맥락 (Context)
* 왜 이 결정이 필요했는가?
* 기존 구조의 한계점 및 발견된 문제점 기술

---

## 💡 선택지 (Alternatives)
1. **기존 방식 유지 및 단순 보완**
   - *장점:* - *단점:* 2. **새로운 방식 도입 (채택)**
   - *장점:* - *단점:* ---

## 🎯 결정 (Decision)
* 어떤 선택지를 최종 채택했는가?

---

## ⚖️ 근거 (Consequences)
* 왜 이 선택지를 골랐는가? (트레이드오프 기재)

---

## 🚀 결과 (Results)
* 적용 후 발생한 변화, 발견한 이슈
* 성능 테스트 결과 또는 코드 가독성 향상 피드백