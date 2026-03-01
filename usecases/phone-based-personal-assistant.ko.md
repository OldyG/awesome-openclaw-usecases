# 전화 기반 개인 비서

## 문제점

스마트폰 앱이나 인터넷 브라우저 없이도 어떤 전화기에서든 AI 에이전트에 접근하고 싶습니다. 운전 중, 걸을 때, 또는 손이 바쁠 때 핸즈프리 음성 지원이 필요합니다.

## 기능 소개

ClawdTalk은 OpenClaw가 전화를 받고 걸 수 있게 해주어, 어떤 전화기든 AI 비서로 가는 관문으로 만들어줍니다. 다음과 같은 것들이 가능합니다:
- 전화번호로 전화를 걸어 AI 에이전트와 음성으로 대화
- 음성으로 캘린더 알림, Jira 업데이트, 웹 검색 결과 받기
- 안정적인 전화 연결을 위한 Telnyx 연동

SMS 지원은 곧 추가될 예정입니다.

## 프롬프트

```
You are available via phone. When I call, greet me and ask how I can help.

For calendar queries: "What's on my calendar today?"
For Jira updates: "Show me my open tickets"
For web search: "Search for latest news on AI agents"
```

## 필요한 스킬

- [ClawdTalk](https://github.com/team-telnyx/clawdtalk-client)
- 캘린더 스킬 (Google Calendar 또는 Outlook)
- Jira 스킬
- 웹 검색 스킬

## 관련 링크

- [ClawdTalk Website](https://clawdtalk.com)
- [ClawdTalk GitHub](https://github.com/team-telnyx/clawdtalk-client)
- [Telnyx API](https://telnyx.com/)
- [OpenClaw Skills](https://github.com/openclaw/skills)
