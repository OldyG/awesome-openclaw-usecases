# YouTube 콘텐츠 파이프라인

일일 YouTube 크리에이터로서 웹과 X/Twitter 전반에서 신선하고 시기적절한 영상 아이디어를 찾는 일은 많은 시간이 듭니다. 이미 다룬 주제를 추적하면 중복을 막고 트렌드보다 한발 앞서갈 수 있습니다.

이 워크플로우는 콘텐츠 스카우팅과 리서치 파이프라인 전체를 자동화합니다:

• 매시간 cron 작업이 AI 속보(web + X/Twitter)를 스캔하여 Telegram으로 영상 아이디어를 제안합니다
• 조회수 및 주제 분석이 포함된 90일치 비디오 카탈로그를 유지해 동일 주제를 다시 다루는 것을 방지합니다
• 모든 제안은 시맨틱 중복 제거를 위한 벡터 임베딩과 함께 SQLite 데이터베이스에 저장됩니다(같은 아이디어가 반복 제안되지 않음)
• Slack에 링크를 공유하면 OpenClaw이 주제를 조사하고 X에서 관련 게시물을 검색하며 지식베이스를 조회하고, 전체 개요가 포함된 Asana 카드를 생성합니다

## 필요한 스킬

- `web_search` (내장)
- [x-research-v2](https://clawhub.ai) 또는 커스텀 X/Twitter 검색 스킬
- RAG용 [knowledge-base](https://clawhub.ai) 스킬
- Asana 연동(또는 Todoist)
- YouTube Analytics용 `gog` CLI
- 아이디어 제안 수신용 Telegram 토픽

## 설정 방법

1. 영상 아이디어용 Telegram 토픽을 만들고 OpenClaw에 설정합니다.
2. knowledge-base 스킬과 x-research 스킬을 설치합니다.
3. 제안 추적용 SQLite 데이터베이스를 생성합니다:
```sql
CREATE TABLE pitches (
  id INTEGER PRIMARY KEY,
  timestamp TEXT,
  topic TEXT,
  embedding BLOB,
  sources TEXT
);
```
4. OpenClaw에 다음을 프롬프트합니다:
```text
다음 작업을 매시간 실행하도록 cron 작업을 설정하세요:
1. 웹과 X/Twitter에서 AI 속보를 검색합니다
2. 내 90일치 YouTube 카탈로그와 대조합니다(gog를 통해 YouTube Analytics에서 가져오기)
3. 데이터베이스에 저장된 모든 과거 제안과 시맨틱 유사도를 확인합니다
4. 새롭다면 출처와 함께 Telegram의 "video ideas" 토픽으로 아이디어를 제안합니다

또한: Slack의 #ai_trends에 링크를 공유하면 자동으로:
1. 해당 주제를 조사합니다
2. 관련 게시물을 X에서 검색합니다
3. 내 지식베이스를 조회합니다
4. Video Pipeline에 전체 개요가 포함된 Asana 카드를 생성합니다
```
