# 이벤트 게스트 참석 확인

이벤트를 주최합니다 — 디너 파티, 결혼식, 회사 오프사이트 — 게스트 명단의 참석 여부를 확인해야 합니다. 20명 이상에게 직접 전화하는 것은 번거롭습니다: 전화 태그를 하고, 누가 뭐라고 했는지 잊어버리고, 식단 제한이나 동반자 정보를 놓치게 됩니다. 문자는 때때로 통하지만, 사람들은 메시지를 무시합니다. 실제 전화가 훨씬 높은 응답률을 보입니다.

이 유스케이스는 OpenClaw가 [SuperCall](https://clawhub.ai/xonder/supercall) 플러그인을 사용하여 게스트 명단의 각 사람에게 전화를 걸고, 참석 여부를 확인하고, 메모를 수집하고, 모든 것을 요약으로 정리합니다.

## 기능 소개

- 게스트 명단(이름 + 전화번호)을 순회하며 각각에게 전화
- AI가 친근한 페르소나로 이벤트 코디네이터로서 자기소개
- 게스트에게 이벤트 날짜, 시간, 장소를 확인
- 참석 여부를 묻고, 메모를 수집 (식단 제한, 동반자, 도착 시간 등)
- 모든 통화가 끝나면 요약을 작성: 누가 확인했는지, 거절했는지, 받지 않았는지, 그리고 모든 메모

## SuperCall을 사용하는 이유

이 유스케이스는 내장 `voice_call` 플러그인이 아닌 [SuperCall](https://clawhub.ai/xonder/supercall) 플러그인을 특별히 사용합니다. 핵심 차이점: SuperCall은 완전히 독립된 음성 에이전트입니다. 통화 중 AI 페르소나는 **제공한 컨텍스트에만 접근**할 수 있습니다 (페르소나 이름, 목표, 오프닝 멘트). 게이트웨이 에이전트, 파일, 다른 도구 등에는 접근할 수 없습니다.

게스트 참석 확인에 이것이 중요한 이유:

- **안전성**: 전화 상대방이 대화를 통해 에이전트를 조작하거나 접근할 수 없습니다. 프롬프트 인젝션이나 데이터 유출 위험이 없습니다.
- **더 나은 대화**: AI가 단일 집중 작업(참석 확인)에 한정되므로, 범용 음성 에이전트보다 주제에 충실하고 자연스럽게 통화를 처리합니다.
- **일괄 처리 적합**: 여러 사람에게 많은 전화를 겁니다. 통화마다 초기화되는 격리된 페르소나가 딱 필요한 것입니다 — 대화 간 간섭이 없습니다.

## 필요한 스킬

- [SuperCall](https://clawhub.ai/xonder/supercall) — `openclaw plugins install @xonder/supercall`로 설치
- Twilio 계정과 전화번호 (아웃바운드 통화용)
- OpenAI API 키 (GPT-4o Realtime 음성 AI용)
- ngrok (웹훅 터널링용 — 무료 티어로 충분)

전체 설정 방법은 [SuperCall README](https://github.com/xonder/supercall)를 참고하세요.

## 설정 방법

1. [설정 가이드](https://github.com/xonder/supercall#configuration)를 따라 SuperCall을 설치하고 구성하세요. OpenClaw 설정에서 훅이 활성화되어 있는지 확인하세요.

2. 게스트 명단을 준비하세요. 채팅에 직접 붙여넣거나 파일로 보관할 수 있습니다:

```text
Guest List — Summer BBQ, Saturday June 14th, 4 PM, 23 Oak Street

- Sarah Johnson: +15551234567
- Mike Chen: +15559876543
- Rachel Torres: +15555551234
- David Kim: +15558887777
```

3. OpenClaw에 프롬프트하세요:

```text
I need you to confirm attendance for my event. Here are the details:

Event: Summer BBQ
Date: Saturday, June 14th at 4 PM
Location: 23 Oak Street

Here is my guest list:
<paste your guest list here>

For each guest, use supercall to call them. Use the persona "Jamie, event coordinator
for [your name]". The goal for each call is to confirm whether they're attending,
and note any dietary restrictions, plus-ones, or other comments.

After each call, log the result. Once all calls are done, give me a full summary:
- Who confirmed
- Who declined
- Who didn't answer
- Any notes or special requests from each guest
```

4. OpenClaw가 SuperCall을 사용하여 게스트에게 하나씩 전화를 건 후 결과를 정리합니다. 언제든 진행 상황을 물어볼 수 있습니다.

## 핵심 인사이트

- **소규모 테스트부터 시작하세요**: 먼저 2~3명의 게스트로 시도하여 페르소나와 오프닝 멘트가 적절한지 확인하세요. 전체 명단에 전화하기 전에 톤을 조정할 수 있습니다.
- **통화 시간대를 신경 쓰세요**: 너무 이르거나 너무 늦은 시간에 전화를 예약하지 마세요. OpenClaw에게 특정 시간대에만 전화하도록 지시할 수 있습니다.
- **통화 내역을 확인하세요**: SuperCall은 `~/clawd/supercall-logs`에 통화 내역을 기록합니다. 첫 배치가 끝난 후 대화가 어떻게 진행됐는지 살펴보세요.
- **부재중 처리**: 상대방이 전화를 받지 않으면 OpenClaw가 기록해두고, 나중에 재시도할지 문자로 후속 조치할지 결정할 수 있습니다.
- **실제 전화 통화는 비용이 듭니다**: 각 통화는 Twilio 분을 사용합니다. 특히 대규모 게스트 명단의 경우 Twilio 계정에 적절한 한도를 설정하세요.

## 관련 링크

- [SuperCall on ClawHub](https://clawhub.ai/xonder/supercall)
- [SuperCall on GitHub](https://github.com/xonder/supercall)
- [Twilio Console](https://console.twilio.com)
- [OpenAI Realtime API](https://platform.openai.com/docs/guides/realtime)
- [ngrok](https://ngrok.com)
