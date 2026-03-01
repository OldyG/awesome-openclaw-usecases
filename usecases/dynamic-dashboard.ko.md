# 동적 대시보드와 서브 에이전트 생성

정적 대시보드는 데이터가 오래되어 수시로 수동 갱신이 필요합니다. 맞춤형 프론트엔드를 새로 만들거나 API 호출 한도에 걸리지 않으면서 여러 데이터 소스의 실시간 가시성이 필요합니다.

이 워크플로는 서브 에이전트를 생성해 병렬로 데이터를 가져오고 처리하는 라이브 대시보드를 만듭니다:

• 여러 데이터 소스(외부 API, 데이터베이스, GitHub, 소셜 미디어)를 동시에 모니터링합니다
• 블로킹을 피하고 API 부하를 분산하기 위해 각 데이터 소스에 대해 서브 에이전트를 생성합니다
• 결과를 통합하여 통합 대시보드(텍스트, HTML, 또는 Canvas)로 집계합니다
• N분마다 갱신하여 최신 데이터를 반영합니다
• 지표가 임계값을 넘으면 알림을 보냅니다
• 시각화를 위해 데이터베이스에 과거 추세를 보관합니다

## 문제점

맞춤형 대시보드를 만드는 데 몇 주가 걸립니다. 완성될 즈음에는 요구사항이 바뀌어 있습니다. 여러 API를 순차적으로 폴링하면 느리고 호출 한도에 걸립니다. 지금 당장 인사이트가 필요하지, 주말 동안 코딩한 뒤에 얻고 싶지 않습니다.

## 동작 방식

대화식으로 모니터링하려는 항목을 정의합니다: "GitHub 스타, Twitter 언급, Polymarket 거래량, 시스템 상태를 추적해줘." OpenClaw은 각 데이터 소스를 병렬로 가져올 서브 에이전트를 생성하고, 결과를 집계하여 Discord 또는 HTML 파일로 포맷된 대시보드를 전달합니다. 업데이트는 cron 스케줄로 자동 실행됩니다.

예시 대시보드 섹션:
- **GitHub**: stars, forks, open issues, 최근 커밋
- **소셜 미디어**: Twitter 언급, Reddit 토론, Discord 활동
- **마켓**: Polymarket 거래량, 예측 트렌드
- **시스템 상태**: CPU, 메모리, 디스크 사용량, 서비스 상태

## 필요한 기술

- 병렬 실행을 위한 서브 에이전트 생성
- GitHub 지표를 위한 `github` (gh CLI)
- 소셜 데이터용 `bird` (Twitter)
- 외부 API를 위한 `web_search` 또는 `web_fetch`
- 과거 지표 저장을 위한 `postgres`
- 대시보드 렌더링을 위한 Discord 또는 Canvas
- 예약 업데이트를 위한 Cron 작업

## 설정 방법

1. 메트릭 데이터베이스 설정:
```sql
CREATE TABLE metrics (
  id SERIAL PRIMARY KEY,
  source TEXT, -- e.g., "github", "twitter", "polymarket"
  metric_name TEXT,
  metric_value NUMERIC,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE alerts (
  id SERIAL PRIMARY KEY,
  source TEXT,
  condition TEXT,
  threshold NUMERIC,
  last_triggered TIMESTAMPTZ
);
```

2. 대시보드 업데이트용 Discord 채널 생성 (예: #dashboard).

3. OpenClaw에 프롬프트 전달:
```text
You are my dynamic dashboard manager. Every 15 minutes, run a cron job to:

1. Spawn sub-agents in parallel to fetch data from:
   - GitHub: stars, forks, open issues, commits (past 24h)
   - Twitter: mentions of "@username", sentiment analysis
   - Polymarket: volume for tracked markets
   - System: CPU, memory, disk usage via shell commands

2. Each sub-agent writes results to the metrics database.

3. Aggregate all results and format a dashboard:

📊 **Dashboard Update** — [timestamp]

**GitHub**
- ⭐ Stars: [count] (+[change])
- 🍴 Forks: [count]
- 🐛 Open Issues: [count]
- 💻 Commits (24h): [count]

**Social Media**
- 🐦 Twitter Mentions: [count]
- 📈 Sentiment: [positive/negative/neutral]

**Markets**
- 📊 Polymarket Volume: $[amount]
- 🔥 Trending: [market names]

**System Health**
- 💻 CPU: [usage]%
- 🧠 Memory: [usage]%
- 💾 Disk: [usage]%

4. Discord #dashboard에 게시합니다.

5. 알림 조건 확인:
   - GitHub 별 수 변화 > 50 in 1 hour → 나에게 알림
   - 시스템 CPU > 90% → 경고
   - Twitter에서 부정적 감정 급증 → 알림

모든 메트릭을 데이터베이스에 저장해 과거 분석에 사용합니다.
```

4. 선택 사항: Canvas를 사용해 차트와 그래프가 포함된 HTML 대시보드를 렌더링합니다.

5. 과거 데이터 질의: "지난 30일간 GitHub star 증가 추세를 보여줘."

## 관련 링크

- [Parallel Processing with Sub-agents](https://docs.openclaw.ai/subagents)
- [Dashboard Design Principles](https://www.nngroup.com/articles/dashboard-design/)

(End of file - total 113 lines)
