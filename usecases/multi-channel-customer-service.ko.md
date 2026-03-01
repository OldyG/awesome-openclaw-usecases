# 다중 채널 AI 고객 서비스 플랫폼

소규모 사업체는 WhatsApp, Instagram DM, 이메일, Google 리뷰 등 여러 앱을 동시에 관리해야 합니다. 고객은 24시간 즉각적인 응답을 기대하지만, 이를 위해 상시 인력을 고용하는 것은 비용이 많이 듭니다.

이 사용 사례는 모든 고객 접점을 단일 AI 기반 인박스로 통합하여 대신 지능적으로 응답하도록 합니다.

## 기능 요약

- **통합 인박스**: WhatsApp Business, Instagram DM, Gmail, Google Reviews를 한 곳에서 처리
- **AI 자동 응답**: FAQ, 예약 요청, 일반 문의를 자동으로 처리
- **사람에게 인계**: 복잡한 이슈는 에스컬레이션하거나 검토 대상으로 표시
- **테스트 모드**: 실제 고객에 영향 없이 클라이언트에게 시스템을 시연할 수 있음
- **비즈니스 컨텍스트**: 서비스, 요금, 정책으로 사전 학습시켜 응답에 반영

## 실제 비즈니스 사례

Futurist Systems에서는 식당, 클리닉, 살롱과 같은 지역 서비스 업체에 이 시스템을 배포합니다. 한 식당은 응답 시간을 4시간 이상에서 2분 이내로 줄였고, 문의의 80%를 자동으로 처리했습니다.

## 필요 기술

- WhatsApp Business API 통합
- Instagram Graph API (Meta Business 통해)
- Gmail용 `gog` CLI
- 리뷰를 위한 Google Business Profile API
- AGENTS.md에 메시지 라우팅 로직 구성

## 설정 방법

1. **채널 연결**을 OpenClaw 설정에서 수행:
   - WhatsApp Business API (360dialog 또는 공식 API 통해)
   - Instagram via Meta Business Suite
   - Gmail via `gog` OAuth
   - Google Business Profile API 토큰

2. **비즈니스 지식 베이스 생성**:
   - 서비스 및 요금
   - 영업 시간 및 위치
   - FAQ 응답
   - 에스컬레이션 트리거(예: 불만, 환불 요청)

3. **AGENTS.md에 라우팅 로직 구성**:

```text
## Customer Service Mode

When receiving customer messages:

1. Identify channel (WhatsApp/Instagram/Email/Review)
2. Check if test mode is enabled for this client
3. Classify intent:
   - FAQ → respond from knowledge base
   - Appointment → check availability, confirm booking
   - Complaint → flag for human review, acknowledge receipt
   - Review → thank for feedback, address concerns

Response style:
- Friendly, professional, concise
- Match the customer's language (ES/EN/UA)
- Never invent information not in knowledge base
- Sign off with business name

Test mode:
- Prefix responses with [TEST]
- Log but don't send to real channels
```

4. **응답 모니터링을 위한 하트비트(정기 점검)** 설정:

```text
## Heartbeat: Customer Service Check

Every 30 minutes:
- Check for unanswered messages > 5 min old
- Alert if response queue is backing up
- Log daily response metrics
```

## 핵심 통찰

- **언어 감지 중요**: 고객의 언어를 자동으로 감지해 해당 언어로 응답하세요
- **테스트 모드 필수**: 클라이언트는 라이브 전 시스템이 작동하는 것을 보고 싶어합니다
- **인계 규칙**: AI의 범위를 넘어가지 않도록 명확한 에스컬레이션 트리거를 정의하세요
- **응답 템플릿**: 민감한 주제(환불, 불만)에 대해 사전 승인된 템플릿을 사용하세요

## 관련 링크

- [WhatsApp Business API](https://developers.facebook.com/docs/whatsapp)
- [Instagram Messaging API](https://developers.facebook.com/docs/instagram-api/guides/messaging)
- [Google Business Profile API](https://developers.google.com/my-business)
