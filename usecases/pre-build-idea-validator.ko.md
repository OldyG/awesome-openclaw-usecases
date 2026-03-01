# 사전 빌드 아이디어 검증기

OpenClaw가 새로운 것을 빌드하기 전에, GitHub, Hacker News, npm, PyPI, Product Hunt에서 아이디어가 이미 존재하는지 자동으로 확인하고 발견 내용에 따라 접근 방식을 조정합니다.

## 기능

- 코드 작성 전에 5개의 실제 데이터 소스(GitHub, Hacker News, npm, PyPI, Product Hunt)를 스캔합니다
- 혼잡도를 나타내는 `reality_signal` 점수(0-100)를 반환합니다
- 별 개수와 설명을 포함한 상위 경쟁자 목록을 보여줍니다
- 공간이 포화 상태일 때 전환(pivot) 방향을 제안합니다
- 사전 빌드 게이트로 작동합니다: 높은 신호 = 중단하고 논의, 낮은 신호 = 진행

## 문제점

에이전트에게 "AI 코드 리뷰 도구를 만들어줘"라고 말하면 6시간 동안 행복하게 코딩을 합니다. 그런데 이미 GitHub에는 143,000개 이상의 관련 레포가 있고 상위 프로젝트는 53,000 스타를 보유합니다. 에이전트는 확인하지 않는데, 당신이 물어보지 않았고 찾아보는 방법을 알지 못하기 때문입니다. 상당한 시간을 투자한 후에야 경쟁자를 발견하게 됩니다. 이 패턴은 새로운 프로젝트 아이디어마다 반복됩니다.

## 필요한 기술

- [idea-reality-mcp](https://github.com/mnemox-ai/idea-reality-mcp) — 실제 데이터 소스를 스캔하고 경쟁 점수를 반환하는 MCP 서버

## 설정 방법

1. idea-reality-mcp 설치:

```bash
uvx idea-reality-mcp
```

2. OpenClaw 설정에 MCP 서버 추가:

```json
{
  "mcpServers": {
    "idea-reality": {
      "command": "uvx",
      "args": ["idea-reality-mcp"]
    }
  }
}
```

3. OpenClaw 에이전트 지침에 다음을 추가:

```text
새 프로젝트, 기능, 도구를 시작하기 전에 항상 먼저 idea_check를 실행하세요.

규칙:
- 만약 reality_signal > 70: 중단. 상위 3개 경쟁자와 별 개수를 보고하세요.
  계속할지, 전환할지, 포기할지 저에게 물어보세요.
- 만약 reality_signal 30-70: 결과와 pivot_hints를 보여주세요.
  기존 프로젝트가 다루지 않는 틈새 각도를 제안하세요.
- 만약 reality_signal < 30: 빌드를 진행하세요.
  해당 분야가 비어있다는 점을 언급하세요.
- 코드 작성 전에 항상 reality_signal 점수와 상위 경쟁자를 보여주세요.
```

## 실제 예시

**You:** "AI 코드 리뷰용 CLI 도구를 만들어줘"

OpenClaw가 `idea_check("AI code review tool")`을 실행하고 응답합니다:

> **reality_signal: 90/100 (매우 높음)**
>
> 상위 경쟁자:
> 1. Gitea — 53,940 stars
> 2. reviewdog — 9,104 stars
> 3. Danger (Ruby) — 5,649 stars
>
> 이 분야에는 143,000개 이상의 관련 레포가 있습니다. 상위 플레이어들은 성숙하고 자금 지원을 받았습니다.
>
> **전환 제안:**
> - 특정 언어에 집중하세요 (예: Rust/Go 전용 AI 코드 리뷰)
> - 특정 프레임워크를 목표로 하세요 (예: React/Vue 컴포넌트 리뷰)
> - 특정 산업을 겨냥하세요 (예: 금융/의료 코드 규정 준수 리뷰)
>
> 차별화된 각도로 진행할까요, 아니면 다른 것으로 전환할까요?

**You:** "사전 빌드 아이디어 검증을 위한 MCP 서버는 어떤가요?"

> **reality_signal: 8/100 (매우 낮음)**
>
> PyPI나 npm에서 직접적인 경쟁자는 0개로 발견되었습니다. GitHub에는 관련된 레포가 일부 있지만 이 정확한 문제를 해결하는 것은 없습니다.
>
> 이 분야는 상당히 비어 있습니다. 빌드를 진행하세요.

## 변형

- **중대한 결정용 딥 모드**: `depth="deep"`를 사용하면 주요 프로젝트 결정을 위해 5개 소스 모두(GitHub + HN + npm + PyPI + Product Hunt)를 병렬로 스캔합니다.
- **배치 검증**: 해커톤 전에 OpenClaw에 아이디어 10개를 주고 `reality_signal`로 순위를 매기게 하세요 — 점수가 낮을수록 가장 독창적인 기회입니다.
- **먼저 웹 데모 보기**: 설치 없이 워크플로우가 적합한지 확인하려면 [mnemox.ai/check](https://mnemox.ai/check)을 사용해 보세요.

## 주요 인사이트

- 이는 빌드에서 가장 비용이 큰 실수를 예방합니다: 이미 해결된 문제를 다시 해결하는 것.
- `reality_signal`은 LLM 추측이 아니라 실제 데이터(레포 수, 스타 분포, HN 토론량)에 기반합니다.
- 높은 점수는 "절대 빌드하지 말라"는 뜻이 아니라 "차별화하거나 포기하라"는 의미입니다.
- 낮은 점수는 진정한 화이트스페이스가 존재함을 의미합니다. 혼자 빌드하는 사람이 성공할 확률이 높은 곳입니다.

## 관련 링크

- [idea-reality-mcp GitHub](https://github.com/mnemox-ai/idea-reality-mcp)
- [웹 데모](https://mnemox.ai/check) (설치 없이 시도해 보세요)
- [PyPI](https://pypi.org/project/idea-reality-mcp/)
