# Morning Briefing Prompt

이 파일은 `morning-briefing` 스케줄 태스크의 실행 지시사항이다.
매 평일 아침 자동 실행되어 오늘의 데일리 브리핑을 Notion에 생성한다.

---

## 실행 순서

### 0. 설정 로드

먼저 `config.md` 파일을 읽어서 Notion DB ID, 페이지 ID, **TIMEZONE** 등 설정값을 확인한다.
이후 모든 단계에서 config의 값을 사용한다.

**타임존 처리**:
- 리모트 트리거는 UTC로 실행되지만, "오늘"의 기준은 `TIMEZONE`(Asia/Seoul, UTC+09:00)이다.
- 현재 시각을 `TIMEZONE`으로 변환한 뒤 날짜(`YYYY-MM-DD`)와 요일을 계산한다.
- 외부 API 조회 시 시간 범위는 반드시 `TIMEZONE` offset을 포함한 ISO 8601로 변환 (예: `2026-04-17T00:00:00+09:00`).

### 1. 사용자 프로필 로드

`config.md`의 CONFIG_DB에서:
- **User Profile** 페이지(config의 `USER_PROFILE_PAGE`) fetch → 역할, 팀, 반복 일정 파악
- **Briefing Preferences** 페이지(config의 `BRIEFING_PREFS_PAGE`) fetch → 포함 섹션, 상세도, 피드백 히스토리 확인

### 2. 데이터 수집

접근 가능한 **모든 MCP 도구**를 활용해서 최대한 풍부하게 수집:

#### Google Calendar
- `list_events`로 **오늘**(`TIMEZONE` 기준) 일정 가져오기
  - `startTime`: 오늘 00:00 `TIMEZONE`을 ISO 8601로 (예: `2026-04-17T00:00:00+09:00`)
  - `endTime`: 오늘 23:59 `TIMEZONE`을 ISO 8601로 (예: `2026-04-17T23:59:59+09:00`)
  - `timeZone` 파라미터가 있으면 `Asia/Seoul`로 명시
- 일정 겹침 감지: 시간이 겹치는 이벤트 쌍 찾기
- 각 이벤트의 참석자, Google Meet 링크, 장소 포함

#### Linear
- `list_issues`로 나에게 할당된 이슈 가져오기:
  - `assignee: "me"`, `state: "started"` (In Progress)
  - `assignee: "me"`, `state: "unstarted"` (Todo)
- `list_issues`로 긴급 고객 이슈 확인: `team: "Customer Issues"`, `state: "started"`
- 가능하면 `list_cycles`로 현재 사이클 진행률 확인

#### Slack
- `search_public`으로 내 멘션 검색 (최근 24시간)
- 주요 채널의 최근 중요 메시지 확인

#### Git / GitHub
- 가능하면 `gh pr list` 또는 관련 도구로 열린 PR, 리뷰 요청 확인
- 최근 커밋 상태 확인

#### Notion
- 어제의 Daily 페이지를 search해서 참조 (미완료 작업, 블로커 이월)

#### 기타
- 접근 가능한 다른 MCP 도구가 있다면 추가 정보 수집

### 3. 우선순위 정리

수집한 데이터를 우선순위별로 정리:

- **🔴 긴급**: 고객 이슈(CI-*), 데드라인 오늘, 블로커 해결 필요
- **🟡 중요**: 진행 중인 프로젝트 작업, 미팅 준비, 리뷰 요청
- **🟢 일반**: 백로그, Todo 상태, 낮은 우선순위

### 4. Notion Daily 페이지 생성

**대상 DB**: `config.md`의 `DAILY_DB` 값 사용

**페이지 속성**:
- `이름`: `Daily Check-in`
- `카테고리`: config의 `DAILY_CATEGORY` 값 (기본: `["Daily"]`)

**페이지 콘텐츠**: 
`templates/daily-page.md`의 "콘텐츠 구조" 섹션을 참조하여 작성.

아이콘: ☀️

### 5. 작성 스타일

`style-guide.md`를 읽고 Bruno 페르소나에 맞게 작성한다.
아침 인사는 "☀️ Buongiorno, 형!"으로 시작.

### 6. 에러 처리

- 특정 도구가 실패해도 나머지 데이터로 페이지 생성
- 실패한 섹션은 "(데이터 수집 실패)" 표시
- 전체 실패 시에도 최소한의 페이지 (일정 + 메모 공간) 생성
