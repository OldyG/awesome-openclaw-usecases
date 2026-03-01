# 건강 및 증상 추적기

음식 민감성을 파악하려면 장기간 일관된 기록이 필요하지만, 이는 유지하기 번거롭습니다. 기록할 수 있도록 리마인더가 필요하고, 패턴을 찾아줄 분석이 필요합니다.

이 워크플로는 음식과 증상을 자동으로 추적합니다:

• 전용 Telegram 토픽에 음식과 증상을 메시지하면 OpenClaw이 모든 항목을 타임스탬프와 함께 기록합니다
• 하루 3회(아침, 점심, 저녁) 리마인더가 기록을 유도합니다
• 시간이 지나면서 패턴을 분석해 잠재적 유발 요인을 찾아냅니다

## 필요한 기술

- 리마인더용 cron 작업
- 로깅용 Telegram 토픽
- 파일 저장소 (마크다운 로그 파일)

## 설정 방법

1. "health-tracker"(또는 유사 이름)라는 Telegram 토픽을 만드세요.
2. 로그 파일 생성: `~/clawd/memory/health-log.md`
3. OpenClaw에 다음을 프롬프트하세요:
```text
When I message in the "health-tracker" topic:
1. Parse the message for food items and symptoms
2. Log to ~/clawd/memory/health-log.md with timestamp
3. Confirm what was logged

Set up 3 daily reminders:
- 8 AM: "🍳 Log your breakfast"
- 1 PM: "🥗 Log your lunch"
- 7 PM: "🍽️ Log your dinner and any symptoms"

Every Sunday, analyze the past week's log and identify patterns:
- Which foods correlate with symptoms?
- Are there time-of-day patterns?
- Any clear triggers?

Post the analysis to the health-tracker topic.
```

4. 선택 사항: OpenClaw이 알려진 유발 요인을 추적하고 패턴이 나타날 때마다 업데이트하도록 메모리 파일을 추가하세요.
