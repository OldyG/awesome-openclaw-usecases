# OpenClaw + n8n 워크플로 오케스트레이션

AI 에이전트에게 직접 API 키를 관리하게 하고 외부 서비스를 호출하게 하는 것은 보안 사고로 이어질 가능성이 큽니다. 새로운 통합이 추가될 때마다 `.env.local`에 자격증명이 늘어나고, 에이전트가 실수로 유출하거나 오용할 수 있는 표면이 커집니다.

이 사용 사례는 OpenClaw가 모든 외부 API 상호작용을 n8n 워크플로로 위임하는 패턴을 설명합니다. 에이전트는 자격증명을 전혀 다루지 않으며, 모든 통합은 시각적으로 확인할 수 있고 잠가 둘 수 있습니다.

## 문제점

OpenClaw가 모든 것을 직접 처리하면 세 가지 복합적인 문제가 발생합니다:

- **가시성 부족**: 에이전트가 실제로 만든 동작이 JavaScript 스킬 파일이나 셸 스크립트에 숨어 있으면 무엇이 실행되는지 확인하기 어렵습니다
- **자격증명 확산**: 모든 API 키가 에이전트의 환경에 존재하면 한 번의 잘못된 커밋으로 유출될 위험이 있습니다
- **토큰 낭비**: 이메일 전송, 스프레드시트 업데이트 같은 결정론적 하위 작업이 워크플로로 처리될 수 있음에도 LLM 추론 토큰을 소모합니다

## 기능 요약

- **프록시 패턴**: OpenClaw가 수신 웹후크를 가진 n8n 워크플로를 생성하고, 이후 모든 API 상호작용을 해당 웹후크로 호출합니다
- **자격증명 격리**: API 키는 n8n의 자격증명 저장소에 보관되며, 에이전트는 웹후크 URL만 알면 됩니다
- **시각적 디버깅**: 모든 워크플로는 n8n의 드래그 앤 드롭 UI에서 검사할 수 있습니다
- **잠글 수 있는 워크플로**: 워크플로를 빌드하고 테스트한 뒤 잠가 에이전트가 수정하지 못하도록 할 수 있습니다
- **안전 장치 단계**: 외부 호출 실행 전에 n8n에서 검증, 속도 제한, 승인 게이트를 추가할 수 있습니다

## 동작 방식

1. **에이전트가 워크플로를 설계**: 필요한 작업을 OpenClaw에 지시합니다(예: "새 GitHub 이슈에 `urgent` 라벨이 붙으면 Slack으로 메시지를 보내는 워크플로를 만들어줘").
2. **에이전트가 n8n에 워크플로를 생성**: OpenClaw가 n8n의 API를 통해 워크플로를 생성하고, 수신 웹후크 트리거를 포함합니다.
3. **사용자가 자격증명을 추가**: n8n UI에서 Slack 토큰이나 GitHub 토큰을 수동으로 추가합니다.
4. **워크플로를 잠급니다**: 에이전트가 더 이상 수정하지 못하도록 설정합니다.
5. **에이전트가 웹후크를 호출**: 이제부터 OpenClaw는 JSON 페이로드로 `http://n8n:5678/webhook/my-workflow`를 호출하며 API 키를 보지 않습니다.

```text
┌──────────────┐     webhook call      ┌─────────────────┐     API call     ┌──────────────┐
│   OpenClaw   │ ───────────────────→  │   n8n Workflow   │ ─────────────→  │  External    │
│   (agent)    │   (no credentials)    │  (locked, with   │  (credentials   │  Service     │
│              │                       │   API keys)      │   stay here)    │  (Slack, etc)│
└──────────────┘                       └─────────────────┘                  └──────────────┘
```

## 필요 기술

- `n8n` API 접근 (워크플로 생성/트리거용)
- `fetch` 또는 `curl` (웹후크 호출용)
- Docker (사전 구성된 스택 사용 시)
- n8n 자격증명 관리(통합별 수동 1회 설정)

## 설정 방법

### 옵션 1: 사전 구성된 Docker 스택

커뮤니티에서 관리하는 Docker Compose 설정([openclaw-n8n-stack](https://github.com/caprihan/openclaw-n8n-stack))이 공유 Docker 네트워크에서 모든 것을 미리 구성합니다:

```bash
git clone https://github.com/caprihan/openclaw-n8n-stack.git
cd openclaw-n8n-stack
cp .env.template .env
# Add your Anthropic API key to .env
docker-compose up -d
```

이 구성으로 얻을 수 있는 것:
- OpenClaw, 포트 3456에서 접근 가능
- n8n, 포트 5678에서 접근 가능
- OpenClaw가 `http://n8n:5678/webhook/...`를 직접 호출할 수 있는 공유 Docker 네트워크
- 사전 구축된 워크플로 템플릿(다중 LLM 사실 확인, 이메일 분류, 소셜 모니터링)

### 옵션 2: 수동 설정

1. n8n을 설치하세요 (`npm install n8n -g` 또는 Docker로 실행)
2. OpenClaw가 n8n 기본 URL을 알도록 구성하세요
3. 다음을 AGENTS.md에 추가하세요:

```text
## n8n Integration Pattern

When I need to interact with external APIs:

1. NEVER store API keys in my environment or skill files
2. Check if an n8n workflow already exists for this integration
3. If not, create one via n8n API with a webhook trigger
4. Notify the user to add credentials and lock the workflow
5. For all future calls, use the webhook URL with a JSON payload

Workflow naming: openclaw-{service}-{action}
Example: openclaw-slack-send-message

Webhook call format:
curl -X POST http://n8n:5678/webhook/{workflow-name} \
  -H "Content-Type: application/json" \
  -d '{"channel": "#general", "message": "Hello from OpenClaw"}'
```

## 핵심 통찰

- **세 가지 장점**: 가시성(시각적 UI), 보안(자격증명 격리), 성능(결정론적 워크플로는 LLM 토큰을 소모하지 않음)
- **테스트 후 잠금**: "빌드 → 테스트 → 잠금" 사이클이 중요합니다. 잠금을 하지 않으면 에이전트가 워크플로를 조용히 변경할 수 있습니다
- **n8n은 400개 이상의 통합을 제공**: 연결하려는 대부분의 외부 서비스에 이미 n8n 노드가 있어 에이전트가 맞춤형 API 호출을 작성할 필요가 없습니다
- **감사 로그 제공**: n8n은 각 워크플로 실행의 입력/출력 데이터를 기록합니다

## 영감을 준 것

이 패턴은 [Simon Høiberg](https://x.com/SimonHoiberg/status/2020843874382487959)가 설명했습니다. 그가 제시한 이유는 이 접근법이 OpenClaw가 API 상호작용을 직접 처리하게 두는 것보다 우수한 세 가지 근거입니다: n8n의 시각적 UI를 통한 가시성, 자격증명 격리를 통한 보안, 그리고 결정론적 하위 작업을 LLM 호출 대신 워크플로로 실행함으로 인한 성능 향상입니다. [openclaw-n8n-stack](https://github.com/caprihan/openclaw-n8n-stack) 저장소는 이 패턴을 구현한 즉시 실행 가능한 Docker Compose 설정을 제공합니다.

## 관련 링크

- [n8n Documentation](https://docs.n8n.io/)
- [openclaw-n8n-stack (Docker 설정)](https://github.com/caprihan/openclaw-n8n-stack)
- [n8n Webhook Trigger Docs](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/)
