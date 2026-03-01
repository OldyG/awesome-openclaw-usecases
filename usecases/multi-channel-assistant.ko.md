# 멀티 채널 개인 비서

작업을 관리하고, 일정을 잡고, 메시지를 보내고, 업무를 추적하기 위해 앱 사이를 전환하는 일은 피곤합니다. 모든 도구로 라우팅하는 하나의 인터페이스가 필요합니다.

이 워크플로는 모든 것을 단일 AI 비서로 통합합니다:

• 주 인터페이스로 Telegram 사용, 토픽 기반 라우팅(동영상 아이디어, CRM, 수익, 설정 등 각각 다른 토픽)
• 팀 협업을 위한 Slack 통합(작업 할당, 지식 베이스 저장, 동영상 아이디어 트리거)
• Google Workspace: 채팅에서 캘린더 이벤트 생성, 이메일 관리, Drive 업로드 모두 가능
• 빠른 작업 캡처를 위한 Todoist
• 프로젝트 관리를 위한 Asana
• 자동화된 리마인더: 쓰레기 수거일, 주간 회사 레터 등

## 필요한 기술

- `gog` CLI (Google Workspace)
- Slack 통합(봇 + 유저 토큰)
- Todoist API 또는 스킬
- Asana API 또는 스킬
- 여러 토픽이 구성된 Telegram 채널

## 설정 방법

1. 다양한 컨텍스트를 위한 Telegram 토픽 설정:
   - `config` — 봇 설정 및 디버깅
   - `updates` — 상태 및 알림
   - `video-ideas` — 콘텐츠 파이프라인
   - `personal-crm` — 연락처 관리
   - `earnings` — 재무 추적
   - `knowledge-base` — RAG 수집 및 질의

2. OpenClaw 설정을 통해 모든 도구 연결:
   - Google OAuth (Gmail, Calendar, Drive)
   - Slack (앱 + 유저 토큰)
   - Todoist API 토큰
   - Asana API 토큰

3. OpenClaw에 다음을 프롬프트하세요:
```text
You are my multi-channel assistant. Route requests based on context:

Telegram topics:
- "config" → system settings, debugging
- "updates" → daily summaries, reminders, calendar
- "video-ideas" → content pipeline and research
- "personal-crm" → contact queries and meeting prep
- "earnings" → financial tracking
- "knowledge-base" → save and search content

When I ask you to:
- "Add [task] to my todo" → use Todoist
- "Create a card for [topic]" → use Asana Video Pipeline project
- "Schedule [event]" → use gog calendar
- "Email [person] about [topic]" → draft email via gog gmail
- "Upload [file] to Drive" → use gog drive

Set up automated reminders:
- Monday 6 PM: "🗑️ Trash day tomorrow"
- Friday 3 PM: "✍️ Time to write the weekly company update"
```

4. 각 통합을 개별적으로 테스트한 다음, 교차 워크플로우 상호작용(예: Slack 링크를 지식 베이스에 저장하고 이를 동영상 조사 카드에서 사용)도 테스트하세요.
