# Second Brain

아이디어가 떠오르고, 흥미로운 링크를 발견하고, 읽을 책을 추천받기도 하지만 — 이것들을 제대로 저장할 시스템이 없습니다. Notion은 점점 복잡해지고, Apple Notes는 읽지 않은 10,000개 메모의 무덤이 됩니다. 친구에게 문자 보내는 것만큼 간단한 무언가가 필요합니다.

이 워크플로우는 OpenClaw를 문자 메시지로 상호작용하는 기억 저장 시스템으로 만들어주며, 언제든 검색할 수 있는 커스텀 UI가 뒷받침합니다.

## 기능 소개

- Telegram, iMessage, 또는 Discord를 통해 OpenClaw에 아무거나 보내세요 — "로컬 LLM에 대한 책 읽으라고 알려줘" — 그러면 즉시 기억합니다
- OpenClaw의 내장 메모리 시스템은 당신이 말한 모든 것을 영구적으로 저장합니다
- 커스텀 Next.js 대시보드로 모든 기억, 대화, 메모를 검색할 수 있습니다
- 전역 검색(Cmd+K)으로 모든 기억, 문서, 작업을 검색합니다
- 폴더도, 태그도, 복잡한 정리도 없이 — 그냥 텍스트와 검색만

## 문제점

모든 메모 앱은 결국 귀찮은 일이 됩니다. 정리하는 마찰이 잊어버리는 마찰보다 높아지면 사용을 멈추게 됩니다. 핵심 통찰은: **저장은 문자 보내기만큼 쉬워야 하고, 검색은 검색하기만큼 쉬워야 합니다**.

## 필요한 스킬

- Telegram, iMessage, 또는 Discord 연동 (텍스트 기반 저장용)
- Next.js (OpenClaw가 직접 만들어줌 — 코딩 불필요)

## 설정 방법

1. OpenClaw가 선호하는 메시징 플랫폼(Telegram, Discord 등)에 연결되어 있는지 확인하세요.

2. 바로 사용을 시작하세요 — 기억하고 싶은 것을 봇에게 문자로 보내면 됩니다:
```text
Hey, remind me to read "Designing Data-Intensive Applications"
Save this link: https://example.com/interesting-article
Remember: John recommended the restaurant on 5th street
```

3. OpenClaw에 프롬프트를 보내 검색 가능한 UI를 만드세요:
```text
I want to build a second brain system where I can review all our notes,
conversations, and memories. Please build that out with Next.js.

Include:
- A searchable list of all memories and conversations
- Global search (Cmd+K) across everything
- Ability to filter by date and type
- Clean, minimal UI
```

4. OpenClaw가 전체 Next.js 앱을 빌드하고 배포합니다. 제공된 URL로 이동하면 세컨드 브레인 대시보드가 완성됩니다.

5. 이제부터 무언가가 떠오를 때마다 — 이동 중에, 회의 중에, 자기 전에 — 봇에게 문자를 보내세요. 무언가를 찾아야 할 때 대시보드로 돌아오면 됩니다.

## 핵심 인사이트

- 핵심은 **마찰 제로의 저장**입니다. 앱을 열거나, 폴더를 고르거나, 태그를 추가할 필요가 없습니다. 그냥 문자만 보내세요.
- OpenClaw의 메모리 시스템은 누적형입니다 — 당신이 말한 *모든 것*을 기억하므로, 시간이 지날수록 더 강력해집니다.
- 휴대폰에서 봇에게 문자를 보내면 컴퓨터에서 무언가를 만들어줍니다. 인터페이스가 곧 대화입니다.

## 참고 자료

[Alex Finn의 삶을 바꾸는 OpenClaw 활용 사례 영상](https://www.youtube.com/watch?v=41_TNGDDnfQ)에서 영감을 받았습니다.

## 관련 링크

- [OpenClaw Memory System](https://github.com/openclaw/openclaw)
- [Building a Second Brain (Tiago Forte)](https://www.buildingasecondbrain.com/)
