# 프로젝트 상태 관리 시스템: Kanban의 이벤트 기반 대안

전통적인 Kanban 보드는 정적이며 수동 업데이트가 필요합니다. 카드를 이동하는 것을 잊고, 세션 사이에 맥락을 잃으며, 상태 변경 뒤에 있는 "왜"를 추적할 수 없습니다. 프로젝트는 명확한 가시성 없이 표류합니다.

이 워크플로는 Kanban을 이벤트 기반 시스템으로 대체해 프로젝트 상태를 자동으로 추적합니다:

• 전체 이력을 포함해 프로젝트 상태를 데이터베이스에 저장
• 결정, 장애물, 다음 단계, 주요 인사이트 등의 맥락을 캡처
• 이벤트 기반 업데이트: "X를 끝냈고 Y 때문에 막혔음" → 자동 상태 전환
• 자연어 질의: "What's the status of [project]?, Why did we pivot on [feature]?"
• 일일 스탠드업 요약: 어제 무슨 일이 있었는지, 오늘 계획은 무엇인지, 무엇이 막고 있는지
• Git 통합: 추적 가능성을 위해 커밋을 프로젝트 이벤트에 연결

## 문제점

Kanban 보드는 오래되어 버립니다. 작업 대신 카드를 업데이트하느라 시간을 낭비합니다. 맥락이 사라집니다. 세 달 후에는 중요한 결정을 왜 내렸는지 기억하지 못합니다. 코드 변경과 프로젝트 진행 사이의 자동 연결이 없습니다.

## 기능

카드를 드래그하는 대신 비서와 대화하세요: "인증 흐름 완료, 대시보드 작업 시작". 시스템은 이벤트를 기록하고 프로젝트 상태를 업데이트하며 맥락을 보존합니다. "대시보드 진행 상황이 어때?"라고 물으면, 완료된 내용, 다음 단계, 차단 요소 및 이유를 포함한 전체 이야기를 알려줍니다.

Git 커밋은 자동으로 스캔되어 프로젝트에 연결됩니다. 일일 스탠드업 요약은 자동으로 작성됩니다.

## 필요한 기술

- 프로젝트 상태 데이터베이스용 `postgres` 또는 SQLite
- 커밋 추적용 `github` (gh CLI)
- 업데이트 및 질의용 Discord 또는 Telegram
- 일일 요약용 cron 작업
- 병렬 프로젝트 분석을 위한 서브 에이전트

## 설정 방법

1. 프로젝트 상태 데이터베이스 설정:
```sql
CREATE TABLE projects (
  id SERIAL PRIMARY KEY,
  name TEXT UNIQUE,
  status TEXT, -- e.g., "active", "blocked", "completed"
  current_phase TEXT,
  last_update TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE events (
  id SERIAL PRIMARY KEY,
  project_id INTEGER REFERENCES projects(id),
  event_type TEXT, -- e.g., "progress", "blocker", "decision", "pivot"
  description TEXT,
  context TEXT,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE blockers (
  id SERIAL PRIMARY KEY,
  project_id INTEGER REFERENCES projects(id),
  blocker_text TEXT,
  status TEXT DEFAULT 'open', -- "open", "resolved"
  created_at TIMESTAMPTZ DEFAULT NOW(),
  resolved_at TIMESTAMPTZ
);
```
2. 프로젝트 업데이트용 Discord 채널 생성(예: #project-state).

3. OpenClaw에 다음을 프롬프트하세요:
```text
You are my project state manager. Instead of Kanban, I'll tell you what I'm working on conversationally.

When I say things like:
- "Finished [task]" → log a "progress" event, update project state
- "Blocked on [issue]" → create a blocker entry, update project status to "blocked"
- "Starting [new task]" → log a "progress" event, update current phase
- "Decided to [decision]" → log a "decision" event with full context
- "Pivoting to [new approach]" → log a "pivot" event with reasoning

When I ask:
- "What's the status of [project]?" → fetch latest events, blockers, and current phase
- "Why did we decide [X]?" → search events for decision context
- "What's blocking us?" → list all open blockers across projects

Every morning at 9 AM, run a cron job to:
1. Scan git commits from the past 24 hours (via gh CLI)
2. Link commits to projects based on branch names or commit messages
3. Post a daily standup summary to Discord #project-state:
   - What happened yesterday (events + commits)
   - What's planned today (based on current phase and recent conversations)
   - What's blocked (open blockers)

When I'm planning a sprint, spawn a sub-agent to analyze each project's state and suggest priorities.
```

4. 워크플로와 통합하세요: 자연스럽게 비서에게 현재 하는 일을 이야기하면 시스템이 모든 것을 캡처합니다.

## 관련 링크

- [Event Sourcing Pattern](https://martinfowler.com/eaaDev/EventSourcing.html)

- [Why Kanban Fails for Solo Developers](https://blog.nuclino.com/why-kanban-doesnt-work-for-me)
