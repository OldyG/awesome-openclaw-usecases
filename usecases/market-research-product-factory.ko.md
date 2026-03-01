# 시장 조사 & 제품 팩토리

제품을 만들고 싶지만 무엇을 만들어야 할지 모릅니다. 또는 사업을 하고 있고 고객이 어떤 어려움을 겪고 있는지 이해해야 합니다. 이 워크플로우는 Last 30 Days 스킬을 사용하여 Reddit과 X에서 실제 고충을 발굴한 다음, OpenClaw가 그 문제들의 솔루션을 만들게 합니다.

## 기능 소개

- [Last 30 Days](https://github.com/matvanhorde/last-30-days) 스킬을 사용하여 최근 30일간 Reddit과 X에서 모든 주제를 리서치
- 사람들이 올리는 실제 과제, 불만, 기능 요청을 표면화
- 실제 사용자 고충에서 제품 기회를 식별하도록 도움
- 한 단계 더 나아가: OpenClaw에게 해당 과제를 해결하는 MVP를 만들도록 요청
- 코딩 없이 리서치부터 제품까지의 완전한 파이프라인 구축

## 문제점

대부분의 예비 창업가는 "무엇을 만들지"의 문제로 고민합니다. 시장 조사는 전통적으로 포럼, 소셜 미디어, 리뷰 사이트를 수동으로 몇 시간이나 탐색하는 것을 의미합니다. 이것은 발견에서 프로토타입까지 전체 파이프라인을 자동화합니다.

## 필요한 스킬

- Matt Van Horde의 [Last 30 Days](https://github.com/matvanhorde/last-30-days) 스킬
- 리서치 보고서 수신을 위한 Telegram 또는 Discord 연동

## 설정 방법

1. Last 30 Days 스킬을 설치하세요:
```text
Install this skill: https://github.com/matvanhorde/last-30-days
```

2. 원하는 주제로 리서치를 실행하세요:
```text
Please use the Last 30 Days skill to research challenges people are
having with [your topic here].

Organize the findings into:
- Top pain points (ranked by frequency)
- Specific complaints and feature requests
- Gaps in existing solutions
- Opportunities for a new product
```

3. 고충을 선택하고 OpenClaw에게 솔루션을 만들게 하세요:
```text
Build me an MVP that solves [pain point from research].
Keep it simple — just the core functionality.
Ship it as a web app I can share with people.
```

4. 지속적인 시장 인텔리전스를 위해 정기 리서치를 예약하세요:
```text
Every Monday morning, use the Last 30 Days skill to research what
people are saying about [your niche] on Reddit and X. Summarize the
top opportunities and send them to my Telegram.
```

## 실제 사례

```text
"Use the Last 30 Days skill to research challenges people are having with OpenClaw."

Results:
- Setup difficulty: Many users struggle with initial configuration
- Skill discovery: People can't find skills that do what they need
- Cost concerns: Users want cheaper model alternatives

→ "Build me a simple web app that makes OpenClaw setup easier with a guided wizard."

OpenClaw builds the app. You ship it. You have a product.
```

## 핵심 인사이트

- 이것은 **자동 조종 창업**입니다: 문제 발견 → 수요 검증 → 솔루션 구축, 모두 문자 메시지로.
- Last 30 Days 스킬은 정제된 설문 데이터가 아닌 실제의 필터링되지 않은 사용자 감정을 제공합니다.
- 기술적일 필요가 없습니다. OpenClaw가 리서치도 하고 제품도 만듭니다.
- 매주 리서치를 예약하여 시장의 변화하는 고충을 놓치지 마세요.

## 참고 자료

[Alex Finn의 삶을 바꾸는 OpenClaw 활용 사례 영상](https://www.youtube.com/watch?v=41_TNGDDnfQ)과 Matt Van Horde의 [Last 30 Days 스킬](https://github.com/matvanhorde/last-30-days)에서 영감을 받았습니다.

## 관련 링크

- [Last 30 Days Skill](https://github.com/matvanhorde/last-30-days)
- [OpenClaw Skills Directory](https://github.com/openclaw/skills)
