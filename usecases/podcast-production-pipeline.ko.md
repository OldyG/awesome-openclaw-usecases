# 팟캐스트 제작 파이프라인

팟캐스트 아이디어가 있고, 에피소드 주제 백로그까지 있을 수 있습니다. 하지만 게스트 리서치, 개요 작성, 인트로 초안, 쇼 노트 생성, 홍보용 소셜 미디어 포스트 작성 등 제작 오버헤드가 모멘텀을 막습니다. 주제를 맡기고 완전한 제작 패키지를 받아본다면 어떨까요?

이 사용 사례는 에이전트들을 연결해 주제에서 공개 준비 자산까지 전체 팟캐스트 제작 워크플로우를 처리합니다.

## 문제점

개인 진행자와 소규모 팀은 녹음보다 제작에 더 많은 시간을 씁니다. 리서치는 시간이 많이 걸리고, 쇼 노트는 사소하게 취급되며, 소셜 미디어 홍보는 가장 먼저 생략됩니다. 창의적인 부분, 즉 대화 자체는 전체 작업의 약 30%일 뿐입니다. 이 에이전트는 나머지 70%를 처리합니다.

## 기능

- **에피소드 리서치** — 주제나 게스트 이름을 입력하면 배경 리서치, 토킹 포인트, 권장 질문을 정리합니다
- **개요 및 스크립트** — 인트로 스크립트, 세그먼트 전환, 마무리 멘트가 포함된 구조화된 에피소드 개요를 생성합니다
- **쇼 노트** — 녹음 후 트랜스크립트를 처리해 타임스탬프가 있는 쇼 노트를 만들고 언급된 모든 항목의 링크를 포함합니다
- **소셜 미디어 키트** — 에피소드 하이라이트와 인용구를 포함한 X, LinkedIn, Instagram용 홍보 게시물을 생성합니다
- **에피소드 설명** — Spotify, Apple Podcasts, YouTube용 SEO 최적화된 에피소드 설명을 작성합니다

## 필요한 스킬

- 게스트 리서치와 주제 심층 조사를 위한 웹 검색/리서치 스킬
- 트랜스크립트를 읽고 출력 파일을 작성할 수 있는 파일 시스템 접근 권한
- 자산 전달을 위한 Slack, Discord, 또는 Telegram 연동
- 선택 사항: 리서치와 작성 에이전트를 병렬로 실행하기 위한 `sessions_spawn`
- 선택 사항: 경쟁 팟캐스트 모니터링을 위한 RSS 피드 스킬

## 설정 방법

1. 녹음 전 — 리서치와 개요 생성:
```text
I'm recording a podcast episode about [TOPIC]. My guest is [NAME].

Please:
1. Research the guest — their background, recent work, hot takes, and
   anything controversial or interesting they've said publicly.
2. Research the topic — key trends, recent news, common misconceptions,
   and what the audience likely already knows vs. what would surprise them.
3. Generate an episode outline:
   - Cold open hook (1-2 sentences to grab attention)
   - Intro script (30 seconds, casual tone)
   - 5-7 interview questions, ordered from easy/rapport-building to deep/provocative
   - 2-3 "back pocket" questions in case the conversation stalls
   - Closing segment with call-to-action

Save everything to ~/podcast/episodes/[episode-number]/prep/
```

2. 녹음 후 — 쇼 노트 및 홍보 자료 생성:
```text
Here's the transcript for Episode [NUMBER]: [paste or point to file]

Please:
1. Write timestamped show notes — every major topic shift gets a timestamp
   and one-line summary. Include links to anything mentioned (tools, books,
   articles, people).
2. Write an episode description (max 200 words) optimized for podcast
   search. Include 3-5 relevant keywords naturally.
3. Create social media posts:
   - X/Twitter: 3 tweets — one pull quote, one key insight, one question
     to spark discussion. Each under 280 chars.
   - LinkedIn: 1 post, professional tone, 100-150 words.
   - Instagram caption: 1 post with emoji, casual tone, include relevant hashtags.
4. Extract a "highlights" list — the 3 most interesting/surprising moments
   with timestamps.

Save everything to ~/podcast/episodes/[episode-number]/publish/
```

3. 선택 사항 — 경쟁사 모니터링:
```text
Monitor these podcast RSS feeds daily:
- [feed URL 1]
- [feed URL 2]

When a new episode drops that covers a topic relevant to my podcast,
send me a Telegram message with:
- Episode title and link
- One-sentence summary
- Whether this is something I should respond to or cover from my angle
```

## 주요 인사이트

- **녹음 전 리서치**가 대부분의 가치를 만듭니다. 게스트에 대해 깊게 조사하고 인터뷰에 임하면 대화의 질이 극적으로 좋아집니다. 이는 사후 제작으로는 흉내낼 수 없습니다.
- 타임스탬프가 포함된 쇼 노트는 청취자 유지에 큰 도움이 됩니다. 대부분의 팟캐스터가 번거로워서 건너뛰는 부분을 이 에이전트가 손쉽게 처리합니다.
- 소셜 미디어 키트는 반복적으로 드는 시간을 가장 많이 절약해 줍니다. 각 에피소드마다 홍보가 필요하고 구조가 항상 비슷하므로 자동화에 적합합니다.
- 팟캐스트 콘텐츠를 블로그 포스트, 뉴스레터, 비디오 클립으로 재활용하려면 [Multi-Agent Content Factory](content-factory.md)와 함께 사용하세요.

## 관련 링크

- [Podcast RSS Feed Spec](https://podcasters.apple.com/support/823-podcast-requirements)
- [Spotify for Podcasters](https://podcasters.spotify.com/)
- [Whisper (OpenAI)](https://github.com/openai/whisper) — for local transcript generation
