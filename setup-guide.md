# Oh My Bruno - Scheduled Tasks Setup Guide

스케줄 태스크 등록용 복붙 가이드. `create_scheduled_task` 또는 Claude Code `/schedule` 명령으로 등록.

## Notion Resources

| 리소스 | ID |
|--------|-----|
| flex kihyun DB (Daily/Weekly) | `collection://3430592a-4a92-8046-9d70-000b43153574` |
| Bruno Config DB (프로필/설정) | `collection://3b28ee66-3d5a-4d64-83fa-6c5cf9206752` |
| Bruno Config DB URL | `https://www.notion.so/c40ad544262e467b94043c6d74db95f7` |
| User Profile Page | `3440592a-4a92-8176-90d2-f268e89e0f8b` |
| Briefing Preferences Page | `3440592a-4a92-81ee-ab3e-f4ecef59654a` |

---

## Task 1: Morning Briefing

```
taskId: morning-briefing
description: 평일 아침 데일리 브리핑 생성 (Calendar + Linear + Slack + Git → Notion)
cronExpression: 57 7 * * 1-5
prompt: |
  /Users/kihyun/Workspace/mini/oh-my-bruno/prompts/morning-briefing.md 파일을 읽고 그 안의 지시사항을 따라 실행해.
  
  이 파일은 Oh My Bruno 개인 비서 시스템의 아침 브리핑 프롬프트야.
  Bruno Config DB에서 사용자 프로필과 설정을 먼저 가져온 뒤, 
  모든 접근 가능한 MCP 도구를 활용해서 데이터를 수집하고 Notion Daily 페이지를 생성해.
```

---

## Task 2: Evening Review

```
taskId: evening-review
description: 평일 저녁 데일리 회고 + 사용자 피드백 반영 (→ Notion Daily 페이지에 append)
cronExpression: 3 19 * * 1-5
prompt: |
  /Users/kihyun/Workspace/mini/oh-my-bruno/prompts/evening-review.md 파일을 읽고 그 안의 지시사항을 따라 실행해.
  
  이 파일은 Oh My Bruno 개인 비서 시스템의 저녁 회고 프롬프트야.
  오늘 생성된 Daily 페이지를 찾아서, 사용자가 남긴 피드백을 읽고 반영한 뒤,
  저녁 회고 섹션을 append해.
```

---

## Task 3: Weekly Summary

```
taskId: weekly-summary
description: 금요일 주간 서머리 생성 (이번 주 데일리 종합 → Notion 주간 페이지)
cronExpression: 7 18 * * 5
prompt: |
  /Users/kihyun/Workspace/mini/oh-my-bruno/prompts/weekly-summary.md 파일을 읽고 그 안의 지시사항을 따라 실행해.
  
  이 파일은 Oh My Bruno 개인 비서 시스템의 주간 서머리 프롬프트야.
  이번 주 Daily 페이지들을 종합하고, 모든 접근 가능한 데이터 소스에서 주간 통계를 수집해서
  Notion 주간 서머리 페이지를 생성해.
```
