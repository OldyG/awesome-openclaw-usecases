# Subagents를 활용한 자율 프로젝트 관리

여러 병렬 워크스트림이 있는 복잡한 프로젝트를 관리하는 것은 매우 피곤합니다. 끊임없이 컨텍스트를 전환하고 여러 도구에서 상태를 추적하며 수동으로 인수인계를 조율하게 됩니다.

이 사용 사례는 중앙 오케스트레이터 대신 공유 상태 파일을 통해 서브에이전트들이 자율적으로 작업하고 조정하는 분산 프로젝트 관리 패턴을 구현합니다.

## 문제점

전통적인 오케스트레이터 패턴은 병목을 만듭니다. 메인 에이전트가 트래픽 경찰이 되어버리죠. 다중 저장소 리팩터링, 연구 스프린트, 콘텐츠 파이프라인 같은 복잡한 프로젝트에서는 지속적인 감독 없이 병렬로 작업할 수 있는 에이전트가 필요합니다.

## 기능 요약

- **분산 조정**: 에이전트가 공유 `STATE.yaml` 파일을 읽고 씁니다
- **병렬 실행**: 여러 서브에이전트가 독립적인 작업을 동시에 수행합니다
- **오케스트레이터 오버헤드 없음**: 메인 세션은 얇게 유지됩니다(CEO 패턴 — 전략만 담당)
- **자가 문서화**: 모든 작업 상태가 버전 관리되는 파일에 남습니다

## 핵심 패턴: STATE.yaml

각 프로젝트는 단일 진실 소스 역할을 하는 `STATE.yaml` 파일을 유지합니다:

```yaml
# STATE.yaml - Project coordination file
project: website-redesign
updated: 2026-02-10T14:30:00Z

tasks:
  - id: homepage-hero
    status: in_progress
    owner: pm-frontend
    started: 2026-02-10T12:00:00Z
    notes: "Working on responsive layout"
    
  - id: api-auth
    status: done
    owner: pm-backend
    completed: 2026-02-10T14:00:00Z
    output: "src/api/auth.ts"
    
  - id: content-migration
    status: blocked
    owner: pm-content
    blocked_by: api-auth
    notes: "Waiting for new endpoint schema"

next_actions:
  - "pm-content: Resume migration now that api-auth is done"
  - "pm-frontend: Review hero with design team"
```

## 동작 방식

1. **메인 에이전트가 작업을 수신** → 특정 범위로 서브에이전트를 생성
2. **서브에이전트가 STATE.yaml을 읽음** → 할당된 작업을 찾음
3. **서브에이전트가 자율적으로 작업** → 진행 상황을 STATE.yaml에 업데이트
4. **다른 에이전트들이 STATE.yaml을 폴링** → 차단 해제된 작업을 가져감
5. **메인 에이전트가 주기적으로 체크인** → 상태 검토 및 우선순위 조정

## 필요 기술

- 서브에이전트 관리용 `sessions_spawn` / `sessions_send`
- STATE.yaml 접근을 위한 파일 시스템 권한
- 상태 버전 관리를 위한 Git (선택 사항이지만 권장)

## 설정: AGENTS.md 구성

```text
## PM Delegation Pattern

Main session = coordinator ONLY. All execution goes to subagents.

Workflow:
1. New task arrives
2. Check PROJECT_REGISTRY.md for existing PM
3. If PM exists → sessions_send(label="pm-xxx", message="[task]")
4. If new project → sessions_spawn(label="pm-xxx", task="[task]")
5. PM executes, updates STATE.yaml, reports back
6. Main agent summarizes to user

Rules:
- Main session: 0-2 tool calls max (spawn/send only)
- PMs own their STATE.yaml files
- PMs can spawn sub-subagents for parallel subtasks
- All state changes committed to git
```

## 예시: PM 생성

```text
User: "Refactor the auth module and update the docs"

Main agent:
1. Checks registry → no active pm-auth
2. Spawns: sessions_spawn(
     label="pm-auth-refactor",
     task="Refactor auth module, update docs. Track in STATE.yaml"
   )
3. Responds: "Spawned pm-auth-refactor. I'll report back when done."

PM subagent:
1. Creates STATE.yaml with task breakdown
2. Works through tasks, updating status
3. Commits changes
4. Reports completion to main
```

## 핵심 통찰

- **STATE.yaml > 오케스트레이터**: 파일 기반 조정은 메시지 전달 방식보다 더 잘 확장됩니다
- **감사 로그로서의 Git**: STATE.yaml 변경을 커밋해 전체 히스토리를 남깁니다
- **라벨 규약이 중요**: 추적을 쉽게 하기 위해 `pm-{project}-{scope}`를 사용하세요
- **얇은 메인 세션**: 메인 에이전트가 적게 할수록 응답 속도가 빨라집니다

## 기반

이 패턴은 자율 코딩 에이전트에 대한 [Nicholas Carlini의 접근법](https://nicholas.carlini.com/)에서 영감을 받았습니다 — 에이전트가 스스로 조직하도록 하고 세세하게 관리하지 마세요.

## 관련 링크

- [OpenClaw Subagent Docs](https://github.com/openclaw/openclaw)
- [Anthropic: Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
