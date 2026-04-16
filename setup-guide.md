# Oh My Bruno - Setup Guide

## Notion Resources

| 리소스 | ID |
|--------|-----|
| flex kihyun DB (Daily/Weekly) | `collection://3430592a-4a92-8046-9d70-000b43153574` |
| Bruno Config DB (프로필/설정) | `collection://3b28ee66-3d5a-4d64-83fa-6c5cf9206752` |
| Bruno Config DB URL | `https://www.notion.so/c40ad544262e467b94043c6d74db95f7` |
| User Profile Page | `3440592a-4a92-8176-90d2-f268e89e0f8b` |
| Briefing Preferences Page | `3440592a-4a92-81ee-ab3e-f4ecef59654a` |

---

## Remote Triggers (클라우드 실행)

관리: https://claude.ai/code/scheduled

### Trigger 1: Morning Briefing
| 항목 | 값 |
|------|-----|
| ID | `trig_011mmitzQZMJUXSsj1YZ7rGB` |
| 스케줄 | 평일 08시 KST (`57 22 * * 0-4` UTC) |
| 모델 | claude-sonnet-4-6 |
| 레포 | https://github.com/flex-kihyun/oh-my-bruno |
| 관리 | https://claude.ai/code/scheduled/trig_011mmitzQZMJUXSsj1YZ7rGB |

### Trigger 2: Evening Review
| 항목 | 값 |
|------|-----|
| ID | `trig_016xz2YYB3cvyJzTUrgusDz4` |
| 스케줄 | 평일 19시 KST (`3 10 * * 1-5` UTC) |
| 모델 | claude-sonnet-4-6 |
| 레포 | https://github.com/flex-kihyun/oh-my-bruno |
| 관리 | https://claude.ai/code/scheduled/trig_016xz2YYB3cvyJzTUrgusDz4 |

### Trigger 3: Weekly Summary
| 항목 | 값 |
|------|-----|
| ID | `trig_015cwwCEFyy8LdJSG5aPzeod` |
| 스케줄 | 금요일 18시 KST (`7 9 * * 5` UTC) |
| 모델 | claude-sonnet-4-6 |
| 레포 | https://github.com/flex-kihyun/oh-my-bruno |
| 관리 | https://claude.ai/code/scheduled/trig_015cwwCEFyy8LdJSG5aPzeod |

---

## MCP 커넥터 설정

리모트 트리거가 Notion, Calendar, Slack, Linear에 접근하려면 https://claude.ai/settings/connectors 에서 커넥터를 연결한 뒤, 각 트리거 설정 페이지에서 해당 커넥터를 연결해야 한다.

필요한 커넥터:
- **Notion** — 페이지 생성/업데이트, DB 검색
- **Google Calendar** — 일정 조회
- **Slack** — 멘션/메시지 검색
- **Linear** — 이슈/프로젝트 조회
