# Oh My Bruno - Personal Assistant System Design

## Context

kihyun(소기현)은 flex 모바일/AI 프로덕트 엔지니어로, 매일 Google Calendar, Linear, Slack, Notion 등 다양한 도구를 사용한다. 이 도구들에 흩어진 정보를 자동으로 수집/정리하고, 사용자 맞춤형으로 진화하는 개인 비서 시스템을 구축한다.

**비서 정체성**: Bruno (1999년생 이탈리아인, 반말 사용, 이탈리아어 표현 혼용)
**목표**: 아침 브리핑 → 저녁 회고 → 주간 서머리 자동화 + 피드백 기반 진화

---

## Architecture

```
oh-my-bruno repo (지침/스킬)
  ├─ CLAUDE.md
  ├─ prompts/        ← 스케줄 태스크 프롬프트
  ├─ templates/       ← Notion 페이지 템플릿
  └─ setup-guide.md  ← 태스크 등록용 복붙 가이드
        │
        │ 읽기 (실행 시 참조)
        ▼
Scheduled Tasks (Claude Code 세션)
  ├─ morning-briefing (평일 아침)
  ├─ evening-review (평일 저녁)
  └─ weekly-summary (금요일)
        │
        │ 수집 & 기록
        ▼
┌─────────────────┐    ┌─────────────────────┐
│ Data Sources     │    │ Notion (데이터 허브)  │
│ · Google Cal     │    │ ├─ flex kihyun DB    │
│ · Linear         │    │ │  (Daily/Weekly)    │
│ · Slack          │    │ └─ Bruno Config DB   │
│ · Notion         │    │    (프로필/설정)      │
│ · Git/Sessions   │    └─────────────────────┘
│ · Figma (선택)   │
│ · Web (선택)     │
└─────────────────┘
```

**핵심 원칙**:
- 레포 = 지침/스킬/템플릿 (코드처럼 관리)
- Notion = 모든 데이터 (output + user profile + config)
- Scheduled Tasks = 실행 엔진

---

## Notion Data Structure

### 기존 DB: flex kihyun (`collection://3430592a-4a92-8046-9d70-000b43153574`)

스키마 변경 없이 그대로 사용.

| 속성 | 타입 | 용도 |
|------|------|------|
| 이름 | title | 페이지 제목 |
| 카테고리 | multi_select | "Daily" / "목표/회고" 등 |
| 생성일 | created_time | 자동 |
| 최종 업데이트 | last_edited_time | 자동 |

### 신규 DB: Bruno Config (Private)

사용자 프로필과 비서 설정 저장. **Private workspace-level 페이지로 생성** (parent 생략).

| 속성 | 타입 | 설명 |
|------|------|------|
| 이름 | title | "User Profile", "Briefing Config" 등 |
| 카테고리 | select | "profile" / "config" / "preferences" |

**페이지 구성**:
- **User Profile**: 역할, 팀, Linear/Slack/Calendar 연동 정보, 업무 패턴
- **Briefing Preferences**: 포함 섹션, 상세도, 우선순위 기준
- **Work Patterns**: 주요 프로젝트, 반복 일정, 집중 시간대

---

## Repo Structure

```
oh-my-bruno/
  CLAUDE.md                     # 프로젝트 지침
  README.md                     # 개요
  setup-guide.md                # 스케줄 태스크 등록 복붙 가이드
  
  prompts/
    morning-briefing.md         # 아침 브리핑 전체 지시사항
    evening-review.md           # 저녁 회고 지시사항
    weekly-summary.md           # 주간 서머리 지시사항
  
  templates/
    daily-page.md               # 데일리 Notion 페이지 콘텐츠 템플릿
    weekly-page.md              # 위클리 Notion 페이지 콘텐츠 템플릿
```

---

## Scheduled Tasks

### 1. morning-briefing
- **스케줄**: `57 7 * * 1-5` (평일 오전 ~8시 KST)
- **프롬프트**: 부트스트랩 — 레포의 `prompts/morning-briefing.md` 읽고 실행

**데이터 수집 (모든 접근 가능한 소스 활용)**:
1. **Google Calendar** → `list_events` (오늘 일정, 일정 겹침 감지)
2. **Linear** → `list_issues` (assignee: "me", state: in_progress/todo), `list_cycles` (현재 사이클 진행률)
3. **Slack** → `search_public` (내 멘션/DM, 최근 24시간), 중요 채널 최근 메시지
4. **Notion** → Bruno Config에서 사용자 프로필 fetch, 어제 Daily 페이지 참조
5. **Git** → 최근 커밋/PR 상태 (gh CLI)
6. **기타** → 접근 가능한 모든 MCP 도구 활용

**출력 구조**:
```markdown
## ☀️ Buongiorno, 형!

## 📅 오늘 일정
- 시간 — 일정명 (장소/링크)
> ⚠️ 일정 겹침 주의: ...

## 🔥 오늘의 할 일 (우선순위순)
### 🔴 긴급
### 🟡 중요
### 🟢 일반

## 💬 Slack 주요 멘션/알림

## 🔧 Git/PR 상태

## 📝 메모
(빈 공간 — 수동 메모용)

## 💬 오늘의 피드백
> 하루 중 아무 때나 여기에 자유롭게 메모해줘.
> 저녁 회고 때 Bruno가 읽고 반영할거야!
- 오늘 브리핑에서 가장 유용했던 것:
- 불필요했던 것:
- 추가로 알고 싶은 것:
- 기타 메모:
```

### 2. evening-review
- **스케줄**: `3 19 * * 1-5` (평일 오후 ~7시 KST)
- **프롬프트**: 부트스트랩 — 레포의 `prompts/evening-review.md` 읽고 실행

**데이터 수집**:
1. 오늘 아침 Daily 페이지 fetch (오늘 할 일과 비교)
2. **사용자 피드백 읽기** → Daily 페이지의 "💬 오늘의 피드백" 섹션에서 사용자가 작성한 내용 파싱
3. **Linear** → 오늘 상태 변화한 이슈들
4. **Google Calendar** → 실제 참석한 미팅
5. **Slack** → 오늘 보낸/받은 주요 메시지
6. **Git** → 오늘 커밋/PR 머지 현황

**피드백 반영 흐름**:
- 아침 Daily 페이지의 "💬 오늘의 피드백" 섹션에 사용자가 하루 중 자유롭게 메모
- 저녁 review 태스크가 이 피드백을 읽어서:
  - "유용했던 것" → 다음 브리핑에서 해당 섹션 유지/강화
  - "불필요했던 것" → Bruno Config의 Briefing Preferences에 반영
  - "추가로 알고 싶은 것" → 다음 브리핑에 해당 데이터 추가 수집
  - "기타 메모" → 저녁 회고에 컨텍스트로 포함
- 피드백 내용을 요약해서 저녁 회고 섹션에 "📋 피드백 반영" 블록으로 기록
- 반복되는 패턴이 있으면 Bruno Config DB의 Briefing Preferences 페이지를 업데이트

**출력 (같은 Daily 페이지에 append)**:
```markdown
---
## 🌙 저녁 회고

## 📋 피드백 반영
> 형이 오늘 남긴 피드백 기반으로 정리했어:
- (사용자 피드백 요약 및 반영 내용)

## ✅ 오늘 완료한 것
## 🔄 진행 중 (내일 계속)
## 🚧 블로커
## 📊 오늘의 숫자
- Linear: N개 완료, N개 진행 중
- 미팅: N개 참석 (총 N시간)
- 커밋: N개
```

### 3. weekly-summary
- **스케줄**: `7 18 * * 5` (금요일 오후 ~6시 KST)
- **프롬프트**: 부트스트랩 — 레포의 `prompts/weekly-summary.md` 읽고 실행

**데이터 수집**:
1. **Notion** → 이번 주 Daily 페이지들 (카테고리 "Daily", 최근 7일)
2. **Linear** → 이번 주 완료/생성 이슈, 사이클 통계
3. **Google Calendar** → 주간 미팅 통계
4. **Slack** → 주간 활동 요약
5. **Git** → 주간 커밋/PR 통계

**출력 구조**:
```markdown
## 📊 주간 서머리 | YYYY-MM-DD ~ MM-DD

## 🏆 이번 주 성과
## ⏰ 시간 분배
## 🔄 이번 주 패턴
## 🎯 다음 주 포커스
## 💡 인사이트
```

카테고리: "목표/회고"

---

## Bruno Config DB 초기 데이터

### User Profile 페이지
```markdown
# 소기현 (kihyun)

## 기본 정보
- 역할: Product Engineer (iOS), Mobile Team @ flex
- Slack User ID: U07EKEJHUTH
- 주요 프로젝트: flexCEO, flex mini

## Linear 팀/프로젝트
- AI Division, Customer Issues, Mobile Team
- 주요 프로젝트: flexCEO (화면 잠금 기능 등)

## 반복 일정
- 11:40 모바일 팀 스탠드업
- 12:00 flex Desk & CEO daily standup

## 업무 패턴
- 오전: 스탠드업 + 이슈 대응
- 오후: 집중 개발 시간
- 고객 이슈(CI-*)가 프로젝트 작업을 자주 밀어냄
```

---

## setup-guide.md (태스크 등록 가이드)

레포에 스케줄 태스크 등록용 복붙 가이드를 포함한다. 각 태스크의:
- `taskId`
- `description`
- `cronExpression`
- `prompt` (부트스트랩 프롬프트 전문)

을 바로 복사해서 `create_scheduled_task`에 넣을 수 있도록 정리.

---

## Implementation Phases

### Phase 1: Foundation
1. 레포 CLAUDE.md 작성
2. Notion Bruno Config DB 생성 + 초기 페이지 작성
3. setup-guide.md 작성

### Phase 2: Morning Briefing
4. `prompts/morning-briefing.md` 작성
5. `templates/daily-page.md` 작성
6. `morning-briefing` 스케줄 태스크 등록
7. 수동 테스트 실행

### Phase 3: Evening Review
8. `prompts/evening-review.md` 작성
9. `evening-review` 스케줄 태스크 등록
10. 수동 테스트 실행

### Phase 4: Weekly Summary
11. `prompts/weekly-summary.md` 작성
12. `templates/weekly-page.md` 작성
13. `weekly-summary` 스케줄 태스크 등록
14. 수동 테스트 실행

### Phase 5 (후속): Feedback Loop
- Notion DB에 피드백용 체크박스 속성 추가
- `feedback-processor` 스케줄 태스크 추가
- 피드백 기반 프롬프트/템플릿 자동 수정

---

## Verification

각 단계별 검증:
1. **Foundation**: Notion Bruno Config DB가 올바르게 생성되었는지 fetch로 확인
2. **Morning Briefing**: 수동으로 태스크 실행 → Notion에 Daily 페이지 생성 확인 → 각 섹션 데이터 정확성 확인
3. **Evening Review**: 수동 실행 → 같은 Daily 페이지에 저녁 회고 append 확인
4. **Weekly Summary**: 수동 실행 → 별도 주간 서머리 페이지 생성 확인
5. **End-to-end**: 스케줄 등록 후 다음 날 자동 실행 확인

---

## Key File References

- Notion flex kihyun DB: `collection://3430592a-4a92-8046-9d70-000b43153574`
- Notion DB URL: `https://www.notion.so/3430592a4a9280efa34edea6c7976d24`
- Slack User ID: `U07EKEJHUTH`
- Repo path: `/Users/kihyun/Workspace/mini/oh-my-bruno`
- Worktree path: `/Users/kihyun/Workspace/mini/worktrees/oh-my-bruno/jovial-hodgkin`
