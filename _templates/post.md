<%*
// ─────────────────────────────────────────────
// 설정: 노트가 저장될 폴더명 (Dataview 인덱싱용)
const FOLDER = "_posts";
// ─────────────────────────────────────────────

const dv = app.plugins.plugins["dataview"]?.api;

// 지정 폴더의 노트들에서 특정 frontmatter 필드의 유니크 값 수집
async function getUniqueValues(field) {
    if (!dv) return [];
    // 빽틱(`) 문법으로 FOLDER 변수가 정상 컴파일되도록 수정
    const pages = dv.pages(`"${FOLDER}"`); 
    const values = new Set();

    pages.forEach(p => {
        const val = p[field];
        if (!val) return;
        if (Array.isArray(val)) {
            val.forEach(v => v && values.add(String(v).trim()));
        } else {
            if (typeof val === 'object') return; // 예외 방어 코드
            values.add(String(val).trim());
        }
    });
    return [...values].filter(Boolean).sort();
}

// 기존 값 목록 + 새로 입력 옵션으로 선택
async function pickOrNew(existing, promptText) {
    const options = existing.length > 0 ? [...existing, "+ 새로 입력"] : ["+ 새로 입력"];
    const pick = await tp.system.suggester(options, options);

    if (!pick || pick === "+ 새로 입력") {
        return (await tp.system.prompt(promptText)) ?? "";
    }
    return pick;
}

// 태그 다중 선택 (완료 선택할 때까지 반복)
async function pickTags(existing) {
    const selected = [];
    while (true) {
        const remaining = [...existing.filter(t => !selected.includes(t)), "+ 새 태그 입력", "✅ 완료"];
        const pick = await tp.system.suggester(remaining, remaining);

        // 버그 패치: 대입(=)을 비교(===) 연산자로 전면 수정
        if (!pick || pick === "✅ 완료") break;

        if (pick === "+ 새 태그 입력") {
            const newTag = await tp.system.prompt("새 태그 입력");
            if (newTag?.trim()) selected.push(newTag.trim());
        } else {
            selected.push(pick);
        }
    }
    return selected;
}

// 기존 값 수집
const existingCategories = await getUniqueValues("category");
const existingProjects   = await getUniqueValues("project");
const existingTags       = await getUniqueValues("tags");

// 카테고리 설정
const defaultCategories = ["debug", "concept", "cote", "architecture", "qa"];
const categoryOptions   = existingCategories.length > 0 ? existingCategories : defaultCategories;
const category = await pickOrNew(categoryOptions, "카테고리 입력 (debug/concept/cote/architecture/qa)");

// 프로젝트 설정
const projectOptions = [...existingProjects, "없음"];
const projectPick    = await pickOrNew(projectOptions, "프로젝트명 입력");
const project        = (projectPick === "없음" || !projectPick) ? "" : projectPick;

// 태그 설정 및 YAML 인덴트 최적화
const tags = await pickTags(existingTags);
let tagsYaml = "";
if (tags.length > 0) {
    // YAML 규격에 맞게 공백 2칸 들여쓰기 자동 정렬
    tagsYaml = "\n" + tags.map(t => `  - ${t}`).join("\n");
} else {
    tagsYaml = " []";
}
-%>
---
title: "<% tp.file.title %>"
date: <% tp.date.now("YYYY-MM-DD") %>
category: <% category %>
project: <% project %>
tags: <% tagsYaml %>
source: 
---

## 개요


## 내용


## 정리