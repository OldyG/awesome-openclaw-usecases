# 자동 회의록 및 액션 아이템

45분짜리 팀 콜을 방금 끝냈습니다. 이제 요약을 작성하고 액션 아이템을 뽑아 Jira, Linear, 또는 Todoist에 배포해야 합니다 — 수동으로요. 전사본이 도착하는 즉시 에이전트가 이 모든 걸 처리하면 어떨까요?

이 사용 사례는 회의 전사본을 구조화된 노트로 변환하고 자동으로 프로젝트 관리 도구에 작업을 생성합니다.

## 문제점

회의록은 귀찮지만 중요합니다. 대부분 사람은 회의록을 건너뛰거나(문맥 손실) 작성하는 데 20분 이상 소요합니다. 액션 아이템은 누군가의 머릿속이나 채팅 스레드에 묻혀 잊힙니다. 이 에이전트는 "논의했다"와 "추적되고 할당되었다" 사이의 간극을 제거합니다.

## 기능

- **새 전사본을 감지**(Otter.ai 익스포트, Google Meet 전사, Zoom 녹음 요약, 또는 채팅에 붙여넣기 방식)
- **핵심 결정, 논의 주제, 액션 아이템(담당자와 기한 포함)을 추출**
- **Jira, Linear, Todoist, 또는 Notion에 작업 생성** — 적절한 사람에게 회의 컨텍스트와 함께 할당
- **요약을 Slack 또는 Discord에 게시**해 전체 팀이 기록을 보도록 함
- **후속 조치** — 선택적으로 마감 전에 담당자에게 예약 리마인더로 푸시

## 필요한 기술

- 작업 생성용 Jira, Linear, Todoist, 또는 Notion 연동
- 요약 게시용 Slack 또는 Discord 연동
- 전사본 파일 읽기용 파일 시스템 접근
- 후속 리마인더를 위한 스케줄링 / cron
- 선택 사항: Otter.ai, Fireflies.ai, 또는 Google Meet API로 자동 전사 수집

## 설정 방법

1. 전사본 소스를 선택합니다. 가장 단순한 방법은 전사본을 채팅에 붙여넣는 것입니다. 자동화를 위해 폴더 감시나 API 연동을 설정하세요.

2. OpenClaw에 프롬프트 제공:
```text
I just finished a meeting. Here's the transcript:

[paste transcript or point to file]

Please:
1. 간결한 요약 작성(최대 5개 불릿) — 핵심 결정과 주제 포함
2. 모든 액션 아이템을 추출. 각각에 대해 다음을 식별:
   - 해야 할 일
   - 담당자(내 팀의 이름과 매칭)
   - 기한(언급 없으면 "TBD"로 표기)
3. 각 액션 아이템에 대해 Jira 티켓 생성, 적절한 담당자 할당
4. 전체 요약을 Slack의 #meeting-notes에 게시
```

3. 완전 자동 파이프라인(전사본 폴더 감시)의 경우:
```text
Set up a recurring task: every 30 minutes, check ~/meeting-transcripts/ for
new .txt or .vtt files. When you find one:

1. 전사본을 구조화된 요약과 액션 아이템으로 파싱
2. 각 액션 아이템에 대해 Linear에 작업 생성
3. 요약을 #team-updates에 Slack으로 게시
4. 처리된 파일을 ~/meeting-transcripts/processed/로 이동

기한이 있는 액션 아이템마다 마감 하루 전 담당자에게 Slack으로 알림을 설정합니다.
```

4. 출력 포맷 사용자화:
```text
회의 요약을 쓸 때 항상 다음 구조를 사용하세요:
- **날짜 및 참석자** 상단에 기재
- **핵심 결정** — 번호 매긴 목록으로 결정된 사항
- **액션 아이템** — 표 형식: Task, Owner, Deadline, Status
- **미해결 질문** — 후속이 필요한 항목
```

## 주요 통찰

- 진짜 가치는 요약 자체가 아니라 **자동 작업 생성**입니다. 추적되지 않는 회의록은 문서화에 불과합니다.
- 전체 가시성을 위해 [Todoist Task Manager](todoist-task-manager.ko.md) 사용 사례와 함께 사용하세요.
- Zoom 또는 Google Meet에서 추출한 VTT/SRT 자막 파일은 타임스탬프가 포함되어 발언자에 귀속시키는 데 유용합니다.
- 먼저 단순하게 시작하세요(전사본 붙여넣기, 요약 얻기) 그리고 점진적으로 자동화하세요. 출력 품질을 검증하기 전에 파이프라인을 과도하게 설계하지 마세요.

## 관련 링크

- [Otter.ai API](https://otter.ai/)
- [Jira REST API](https://developer.atlassian.com/cloud/jira/platform/rest/v3/)
- [Linear API](https://developers.linear.app/)
- [Slack API](https://api.slack.com/)

(End of file - total 82 lines)
