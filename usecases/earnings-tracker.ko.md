# AI 기반 실적 추적기

수십 개의 테크 기업의 실적 시즌을 팔로우하려면 여러 소스를 확인하고 보고서 날짜를 기억해야 합니다. 모든 기업을 수동으로 추적하지 않고도 AI/테크 실적을 놓치지 않고 싶습니다.

이 워크플로우는 실적 추적과 전달을 자동화합니다:

• 매주 일요일 프리뷰: 다가오는 실적 캘린더를 스캔하고 관련 테크/AI 기업을 Telegram에 게시
• 관심 있는 기업을 선택하면, OpenClaw가 각 실적 날짜에 맞는 일회성 cron job을 예약
• 각 보고서가 발표되면, OpenClaw가 결과를 검색하고 상세 요약(예상치 상회/하회, 주요 지표, AI 관련 하이라이트)을 작성하여 전달

## 필요한 스킬

- `web_search` (내장)
- OpenClaw의 cron job 지원
- 실적 업데이트용 Telegram 토픽

## 설정 방법

1. "earnings"라는 이름의 Telegram 토픽을 만드세요.
2. OpenClaw에 프롬프트하세요:
```text
Every Sunday at 6 PM, run a cron job to:
1. Search for the upcoming week's earnings calendar for tech and AI companies
2. Filter for companies I care about (NVDA, MSFT, GOOGL, META, AMZN, TSLA, AMD, etc.)
3. Post the list to my Telegram "earnings" topic
4. Wait for me to confirm which ones I want to track

When I reply with which companies to track:
1. Schedule one-shot cron jobs for each earnings date/time
2. After each report drops, search for earnings results
3. Format a summary including: beat/miss, revenue, EPS, key metrics, AI-related highlights, guidance
4. Post to Telegram "earnings" topic

Keep a memory of which companies I typically track so you can auto-suggest them each week.
```
