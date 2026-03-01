# 다중 출처 기술 뉴스 요약

자동으로 109개 이상의 소스(RSS, Twitter/X, GitHub 릴리스, 웹 검색 등)에서 기술 뉴스를 수집, 점수화하여 자연어로 관리되는 방식으로 배포합니다.

## 문제점

AI, 오픈소스, 첨단 기술 분야의 최신 소식을 따라가려면 수십 개의 RSS 피드, Twitter 계정, GitHub 저장소, 뉴스 사이트를 매일 확인해야 합니다. 수동 큐레이션에는 시간이 많이 들고, 기존 도구들은 품질 필터링이 부족하거나 복잡한 설정이 필요합니다.

## 기능

스케줄에 따라 실행되는 4단계 데이터 파이프라인:

1. **RSS Feeds** (46 sources) — OpenAI, Hacker News, MIT Tech Review, etc.
2. **Twitter/X KOLs** (44 accounts) — @karpathy, @sama, @VitalikButerin, etc.
3. **GitHub Releases** (19 repos) — vLLM, LangChain, Ollama, Dify, etc.
4. **Web Search** (4 topic searches) — via Brave Search API

모든 기사들은 병합되고 제목 유사도로 중복 제거된 후 품질 점수가 매겨집니다 (우선 출처 +3, 다중 출처 +5, 최신성 +2, 참여도 +1). 최종 요약은 Discord, 이메일 또는 Telegram으로 전달됩니다.

이 프레임워크는 완전히 사용자 지정 가능합니다 — 자신의 RSS 피드, Twitter 핸들, GitHub 저장소 또는 검색 쿼리를 30초 안에 추가할 수 있습니다.

## 프롬프트

**일간 요약 설치 및 설정:**
```text
Install tech-news-digest from ClawHub. Set up a daily tech digest at 9am to Discord #tech-news channel. Also send it to my email at myemail@example.com.
```

**Add custom sources:**
```text
Add these to my tech digest sources:
- RSS: https://my-company-blog.com/feed
- Twitter: @myFavResearcher
- GitHub: my-org/my-framework
```

**Generate on demand:**
```text
Generate a tech digest for the past 24 hours and send it here.
```

## 필요한 스킬

- [tech-news-digest](https://clawhub.ai/skills/tech-news-digest) — Install via `clawhub install tech-news-digest`
- [gog](https://clawhub.ai/skills/gog) (optional) — For email delivery via Gmail

## 환경 변수 (선택 사항)

- `X_BEARER_TOKEN` — Twitter/X API bearer token for KOL monitoring
- `BRAVE_API_KEY` — Brave Search API key for web search layer
- `GITHUB_TOKEN` — GitHub token for higher API rate limits

## 관련 링크

- [GitHub Repository](https://github.com/draco-agent/tech-news-digest)
- [ClawHub Page](https://clawhub.ai/skills/tech-news-digest)
