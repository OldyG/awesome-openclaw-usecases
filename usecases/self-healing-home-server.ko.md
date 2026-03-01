# Self-Healing 홈 서버 및 인프라 관리

홈 서버를 운영한다는 것은 자체 인프라에 대해 24시간 대기해야 한다는 의미입니다. 서비스는 새벽 3시에 다운되고, 인증서는 조용히 만료되며, 디스크는 가득 차고, 파드는 크래시 루프에 빠지는 일이 발생합니다. 이런 상황은 대부분 사용자가 잠들어 있거나 자리를 비운 상태에서 일어납니다.

이 사용 사례는 OpenClaw를 SSH 접근 권한과 자동화된 cron 작업을 가진 지속적인 인프라 에이전트로 바꿉니다. 에이전트는 문제를 감지하고 진단하며, 사용자가 인지하기 전에 스스로 문제를 해결할 수 있습니다.

## 문제점

홈랩 운영자와 셀프호스터는 지속적인 유지관리 부담에 직면합니다:

- 상태 점검, 로그 모니터링, 경고 설정은 수동으로 구성하고 주시해야 합니다
- 무언가 고장나면 SSH로 접속해 진단하고 수정해야 하며, 종종 휴대폰으로 작업해야 합니다
- Terraform, Ansible, Kubernetes 매니페스트 같은 인프라 코드도 정기적인 업데이트가 필요합니다
- 시스템 구성에 대한 지식이 머릿속에만 있어 검색 가능한 문서로 남아있지 않습니다
- 이메일 분류, 배포 점검, 보안 감사 같은 반복 작업이 매주 많은 시간을 차지합니다

## 기능 요약

- **자동화된 상태 모니터링**: 서비스, 배포, 시스템 자원에 대한 cron 기반 점검
- **셀프 힐링**: 상태 점검으로 문제를 감지하고 자율적으로 수정(파드 재시작, 리소스 스케일링, 설정 수정)
- **인프라 관리**: Terraform, Ansible, Kubernetes 매니페스트 생성 및 적용
- **모닝 브리핑**: 시스템 상태, 캘린더, 날씨, 태스크 보드 현황의 일일 요약 제공
- **이메일 분류**: 받은편지함을 스캔해 실행 가능한 항목에 라벨을 붙이고 잡음을 보관
- **지식 추출**: 노트와 대화 내역을 구조화된 검색 가능한 지식 베이스로 변환
- **블로그 퍼블리싱 파이프라인**: 초안 → 배너 생성 → CMS 게시 → 호스팅 배포까지 자동화
- **보안 감사**: 하드코딩 비밀, 권한이 높은 컨테이너, 과도한 권한을 정기적으로 검사

## 필요 기술

- 홈 네트워크 머신에 대한 `ssh` 접근
- Kubernetes 클러스터 관리를 위한 `kubectl`
- 인프라스트럭처 코드용 `terraform` 및 `ansible`
- 비밀 관리를 위한 `1password` CLI
- 이메일 접근용 `gog` CLI
- 캘린더 API 접근
- 지식 베이스용 Obsidian 볼트나 노트 디렉토리
- 자체 진단용 `openclaw doctor`

## 설정 방법

### 1. 코어 에이전트 구성

에이전트 이름을 정하고 AGENTS.md에서 접근 범위를 정의하세요:

```text
## Infrastructure Agent

You are Reef, an infrastructure management agent.

Access:
- SSH to all machines on the home network (192.168.1.0/24)
- kubectl for the K3s cluster
- 1Password vault (read-only for credentials, dedicated AI vault)
- Gmail via gog CLI
- Calendar (yours + partner's)
- Obsidian vault at ~/Documents/Obsidian/

Rules:
- NEVER hardcode secrets — always use 1Password CLI or environment variables
- NEVER push directly to main — always create a PR
- Run `openclaw doctor` as part of self-health checks
- Log all infrastructure changes to ~/logs/infra-changes.md
```

### 2. 자동화된 Cron 작업 시스템

이 설정의 핵심은 예약 작업 시스템입니다. HEARTBEAT.md에서 구성하세요:

```text
## Cron Schedule

Every 15 minutes:
- Check kanban board for in-progress tasks → continue work

Every hour:
- Monitor health checks (Gatus, ArgoCD, service endpoints)
- Triage Gmail (label actionable items, archive noise)
- Check for unanswered alerts or notifications

Every 6 hours:
- Knowledge base data entry (process new Obsidian notes)
- Self health check (openclaw doctor, disk usage, memory, logs)

Every 12 hours:
- Code quality and documentation audit
- Log analysis via Loki/monitoring stack

Daily:
- 4:00 AM: Nightly brainstorm (explore connections between notes)
- 8:00 AM: Morning briefing (weather, calendars, system stats, task board)
- 1:00 AM: Velocity assessment (process improvements)

Weekly:
- Knowledge base QA review
- Infrastructure security audit
```

### 3. 보안 설정 (중요)

이는 협상 불가 항목입니다. 에이전트에 SSH 접근 권한을 주기 전에 다음을 수행하세요:

```text
## Security Checklist

1. Pre-push hooks:
   - Install TruffleHog or similar secret scanner on ALL repositories
   - Block any commit containing hardcoded API keys, tokens, or passwords

2. Local-first Git workflow:
   - Use Gitea (self-hosted) for private code before pushing to public GitHub
   - CI scanning pipeline (Woodpecker or similar) runs before any public push
   - Human review required before main branch merges

3. Defense in depth:
   - Dedicated 1Password vault for AI agent (limited scope)
   - Network segmentation for sensitive services
   - Daily automated security audits checking for:
     * Privileged containers
     * Hardcoded secrets in code or configs
     * Overly permissive file/network access
     * Known vulnerabilities in deployed images

4. Agent constraints:
   - Branch protection: PR required for main, agent cannot override
   - Read-only access where write isn't needed
   - All changes logged and auditable via git
```

### 4. 모닝 브리핑 템플릿

```text
## Daily Briefing Format

Generate and deliver at 8:00 AM:

### Weather
- Current conditions and forecast for [your location]

### Calendars
- Your events today
- Partner's events today
- Conflicts or overlaps flagged

### System Health
- CPU / RAM / Storage across all machines
- Services: UP/DOWN status
- Recent deployments (ArgoCD)
- Any alerts in last 24h

### Task Board
- Cards completed yesterday
- Cards in progress
- Blocked items needing attention

### Highlights
- Notable items from nightly brainstorm
- Emails requiring action
- Upcoming deadlines this week
```

## 핵심 통찰

- **"Self-healing 서버가 생긴 것이 믿기지 않는다"**: 에이전트는 SSH, Terraform, Ansible, kubectl 명령을 실행해 사용자가 문제를 인지하기 전에 인프라 문제를 해결할 수 있습니다
- **AI는 비밀을 하드코딩할 것이다**: 이는 가장 큰 보안 위험입니다. 에이전트는 가드레일이 없으면 API 키를 코드에 바로 넣을 수 있습니다. Pre-push 훅과 비밀 스캐닝은 필수입니다
- **로컬 우선 Git이 필수적이다**: 에이전트가 공개 저장소로 직접 푸시하지 못하게 하세요. 프라이빗 Gitea 인스턴스를 스테이징 영역으로 사용하고 CI 스캔을 적용하세요
- **Cron 작업이 핵심 제품이다**: 예약된 자동화(헬스 체크, 이메일 분류, 브리핑)가 일회성 명령보다 매일 더 큰 가치를 제공합니다
- **지식 추출의 누적 효과**: 노트, 대화 내역, 이메일을 구조화된 지식 베이스로 처리하면 시간이 지날수록 더 가치가 커집니다. 한 사용자는 ChatGPT 히스토리에서 49,079개의 원자적 사실을 추출했습니다

## 영감 출처

이 사용 사례는 Nathan의 상세한 정리 ["Everything I've Done with OpenClaw (So Far)"](https://madebynathan.com/2026/02/03/everything-ive-done-with-openclaw-so-far/)를 기반으로 합니다. 그는 홈 서버에서 모든 머신에 대한 SSH 접근, Kubernetes 클러스터, 1Password 통합, 5,000개 이상의 노트를 보유한 Obsidian 볼트를 사용하는 OpenClaw 에이전트 "Reef"를 설명합니다. Reef는 15개의 활성 cron 작업, 24개의 커스텀 스크립트를 실행하고 작업 관리 UI를 포함한 애플리케이션을 자율적으로 빌드 및 배포했습니다. Day 1에 API 키 유출을 겪은 Nathan의 교훈: "AI 어시스턴트는 기꺼이 비밀을 하드코딩한다. 인간과 같은 본능이 항상 있는 것은 아니다." 그의 방어 심층 보안 설정(TruffleHog pre-push 훅, 로컬 Gitea, CI 스캔, 일일 감사)은 이 패턴을 시도하려는 모든 사람이 반드시 읽어야 합니다.

또한 [OpenClaw Showcase](https://openclaw.ai/showcase)에서 `@georgedagg_`가 비슷한 패턴을 설명했습니다: 배포 모니터링, 로그 검토, 설정 수정, PR 제출 — 모두 개 산책 중에도 수행됩니다.

## 관련 링크

- [Nathan's Full Writeup](https://madebynathan.com/2026/02/03/everything-ive-done-with-openclaw-so-far/)
- [OpenClaw Documentation](https://github.com/openclaw/openclaw)
- [TruffleHog (Secret Scanning)](https://github.com/trufflesecurity/trufflehog)
- [K3s (Lightweight Kubernetes)](https://k3s.io/)
- [Gitea (Self-hosted Git)](https://gitea.io/)
- [n8n (Workflow Automation)](https://n8n.io/)
