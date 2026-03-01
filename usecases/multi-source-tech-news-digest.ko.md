# 다중 출처 기술 뉴스 요약

자동으로 109개 이상의 소스(RSS, Twitter/X, GitHub 릴리스, 웹 검색 등)에서 기술 뉴스를 수집하고, 품질 점수를 매긴 뒤 자연어로 관리되는 방식으로 배포합니다.

## 문제점

AI, 오픈소스, 첨단 기술 분야의 최신 소식을 따라가려면 수십 개의 RSS 피드, Twitter 계정, GitHub 저장소, 뉴스 사이트를 매일 확인해야 합니다. 수동 큐레이션에는 시간이 많이 들고, 기존 도구들은 품질 필터링이 부족하거나 복잡한 설정이 필요합니다.

## 기능

스케줄에 따라 실행되는 4단계 데이터 파이프라인:

1. **RSS Feeds** (46개 소스) — OpenAI, Hacker News, MIT Tech Review 등.
2. **Twitter/X KOLs** (44개 계정) — @karpathy, @sama, @VitalikButerin 등.
3. **GitHub Releases** (19개 저장소) — vLLM, LangChain, Ollama, Dify 등.
4. **Web Search** (4개 주제 검색) — Brave Search API를 통해

모든 기사들은 병합되고 제목 유사도로 중복 제거된 후 품질 점수가 매겨집니다 (우선 출처 +3, 다중 출처 +5, 최신성 +2, 참여도 +1). 최종 요약은 Discord, 이메일 또는 Telegram으로 전달됩니다.

이 프레임워크는 완전히 사용자 지정 가능합니다 — 자신의 RSS 피드, Twitter 핸들, GitHub 저장소 또는 검색 쿼리를 30초 안에 추가할 수 있습니다.

## 프롬프트

**일간 요약 설치 및 설정:**
```text
Install tech-news-digest from ClawHub. Set up a daily tech digest at 9am to Discord #tech-news channel. Also send it to my email at myemail@example.com.
```

**맞춤 소스 추가:**
```text
Add these to my tech digest sources:
- RSS: https://my-company-blog.com/feed
- Twitter: @myFavResearcher
- GitHub: my-org/my-framework
```

**요청 시 생성:**
```text
Generate a tech digest for the past 24 hours and send it here.
```

## 필요한 스킬

- [tech-news-digest](https://clawhub.ai/skills/tech-news-digest) — 설치: `clawhub install tech-news-digest`
- [gog](https://clawhub.ai/skills/gog) (선택 사항) — Gmail을 통한 이메일 전송용

## 환경 변수 (선택 사항)

- `X_BEARER_TOKEN` — KOL 모니터링용 Twitter/X API 베어러 토큰
- `BRAVE_API_KEY` — 웹 검색 레이어용 Brave Search API 키
- `GITHUB_TOKEN` — 더 높은 API 요청 한도를 위한 GitHub 토큰

## 관련 링크

- [GitHub 저장소](https://github.com/draco-agent/tech-news-digest)
- [ClawHub 페이지](https://clawhub.ai/skills/tech-news-digest)
