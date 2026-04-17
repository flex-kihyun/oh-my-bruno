# Oh My Bruno - Configuration

이 파일에 사용자별 설정값을 입력한다.
프롬프트 파일들은 이 config를 참조하여 실행된다.

## Timezone

| 항목 | 값 | 설명 |
|------|-----|------|
| TIMEZONE | `Asia/Seoul` | 사용자 기준 타임존 (KST, UTC+09:00) |
| UTC_OFFSET | `+09:00` | ISO 8601 날짜/시간 포맷에 사용할 offset |

**중요**: 리모트 트리거는 UTC로 실행되지만, "오늘", "어제", "이번 주" 등 모든 시간 기준은 `TIMEZONE`을 따른다.
- 날짜 표기(`YYYY-MM-DD`, 요일)는 `TIMEZONE` 기준 현재 날짜
- Calendar/Linear/Slack 조회 시 `startTime`/`endTime`은 `TIMEZONE` 기준 자정을 ISO 8601(`2026-04-17T00:00:00+09:00` 형태)로 변환
- "이번 주"는 `TIMEZONE` 기준 월요일 00:00 ~ 일요일 23:59

## Notion

| 항목 | 값 | 설명 |
|------|-----|------|
| DAILY_DB | `collection://3430592a-4a92-8046-9d70-000b43153574` | Daily/Weekly 페이지를 저장하는 Notion DB |
| CONFIG_DB | `collection://3b28ee66-3d5a-4d64-83fa-6c5cf9206752` | Bruno Config DB (사용자 프로필/설정) |
| USER_PROFILE_PAGE | `3440592a-4a92-8176-90d2-f268e89e0f8b` | User Profile 페이지 ID |
| BRIEFING_PREFS_PAGE | `3440592a-4a92-81ee-ab3e-f4ecef59654a` | Briefing Preferences 페이지 ID |

## Slack

| 항목 | 값 | 설명 |
|------|-----|------|
| SLACK_USER_ID | `U07EKEJHUTH` | Slack 사용자 ID |

## Daily Page Settings

| 항목 | 값 | 설명 |
|------|-----|------|
| DAILY_CATEGORY | `Daily` | 데일리 페이지의 카테고리 |
| WEEKLY_CATEGORY | `목표/회고` | 위클리 페이지의 카테고리 |

## Schedule

모든 시간은 `TIMEZONE`(Asia/Seoul) 기준이다.

| 항목 | 값 | 설명 |
|------|-----|------|
| MORNING_TIME | `08:00` | 아침 브리핑 시간 |
| EVENING_TIME | `19:00` | 저녁 회고 시간 |
| WEEKLY_DAY_TIME | `금요일 18:00` | 주간 서머리 요일/시간 |
