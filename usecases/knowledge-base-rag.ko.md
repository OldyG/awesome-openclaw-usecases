# 개인 지식 베이스 (RAG)

하루 종일 글을 읽고, 트윗을 보고, 영상을 시청하지만 지난주에 본 그것을 도무지 찾을 수 없습니다. 북마크는 쌓이기만 하고 쓸모없어집니다.

이 워크플로우는 저장한 모든 것으로 검색 가능한 지식 베이스를 구축합니다:

• Telegram이나 Slack에 URL을 드롭하면 콘텐츠를 자동으로 수집합니다 (글, 트윗, YouTube 자막, PDF)
• 저장한 모든 것에 대한 시맨틱 검색: "에이전트 메모리에 대해 저장한 게 뭐야?"라고 물으면 출처와 함께 순위별 결과를 반환
• 다른 워크플로우에 연동 — 예: 영상 아이디어 파이프라인이 리서치 카드를 만들 때 KB에서 관련 저장 콘텐츠를 조회

## 필요한 스킬

- [knowledge-base](https://clawhub.ai) 스킬 (또는 임베딩으로 커스텀 RAG 구축)
- `web_fetch` (내장)
- 수집용 Telegram 토픽 또는 Slack 채널

## 설정 방법

1. ClawdHub에서 knowledge-base 스킬을 설치하세요.
2. "knowledge-base"라는 이름의 Telegram 토픽을 만드세요 (또는 Slack 채널 사용).
3. OpenClaw에 프롬프트하세요:
```text
When I drop a URL in the "knowledge-base" topic:
1. Fetch the content (article, tweet, YouTube transcript, PDF)
2. Ingest it into the knowledge base with metadata (title, URL, date, type)
3. Reply with confirmation: what was ingested and chunk count

When I ask a question in this topic:
1. Search the knowledge base semantically
2. Return top results with sources and relevant excerpts
3. If no good matches, tell me

Also: when other workflows need research (e.g., video ideas, meeting prep), automatically query the knowledge base for relevant saved content.
```

4. URL 몇 개를 드롭하고 "LLM 메모리에 대해 뭐가 있어?"같은 질문을 해서 테스트해보세요.
