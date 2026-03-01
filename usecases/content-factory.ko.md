# 멀티 에이전트 콘텐츠 팩토리

콘텐츠 제작자로서 당신은 여러 플랫폼에 걸쳐 리서치, 작성, 디자인을 동시에 처리하느라 바쁩니다. 트렌드 주제 찾기, 스크립트 작성, 썸네일 생성 등 각 단계는 하루를 잡아먹습니다. 전문화된 에이전트 팀이 밤사이에 이 모든 것을 처리한다면 어떨까요?

이 워크플로우는 Discord 내부에 멀티 에이전트 콘텐츠 팩토리를 구축합니다. 각기 다른 에이전트가 전용 채널에서 리서치, 작성, 시각 자산을 담당합니다.

## 주요 기능

- **Research Agent**는 매일 아침 트렌딩 스토리, 경쟁사 콘텐츠, 소셜 미디어를 스캔해 최고의 콘텐츠 기회를 찾습니다
- **Writing Agent**는 상위 아이디어를 받아 전체 스크립트, 스레드, 뉴스레터 초안을 작성합니다
- **Thumbnail Agent**는 콘텐츠용 AI 썸네일이나 커버 이미지를 생성합니다
- 각 에이전트는 자체 Discord 채널에서 작업해 모든 것을 정리하고 검토할 수 있게 합니다
- 예: 매일 오전 8시에 자동으로 실행되므로 아침에 완성된 콘텐츠를 확인할 수 있습니다

## 문제점

콘텐츠 제작은 리서치, 작성, 디자인의 세 단계로 이루어지며 대부분의 제작자는 이 세 가지를 직접 처리합니다. AI 작성 도구가 있더라도 단계별로 하나씩 프롬프트해야 합니다. 이 시스템은 에이전트들을 파이프라인으로 연결해 한 에이전트의 출력이 다음 에이전트의 입력이 되게 하므로 완전한 무인화를 실현합니다.

## 필요한 스킬

- Discord 연동과 다중 채널 설정
- 멀티 에이전트 오케스트레이션을 위한 `sessions_spawn` / `sessions_send`
- 소셜 미디어 리서치를 위한 [x-research-v2](https://clawhub.ai) 또는 유사 스킬
- 로컬 이미지 생성(예: Nano Banana) 또는 이미지 생성 API
- RAG 기반 리서치를 위한 [knowledge-base](https://clawhub.ai) 스킬(선택)

## 설정 방법

1. Discord 서버를 설정합니다(또는 OpenClaw에 "우리용 Discord 설정해줘"라고 요청하세요).

2. 각 에이전트용 채널을 만듭니다:
   - `#research` — 트렌드 주제와 콘텐츠 기회
   - `#scripts` — 작성된 초안과 개요
   - `#thumbnails` — 생성된 이미지와 커버 아트

3. OpenClaw에 다음을 프롬프트합니다:
```text
I want you to build me a content factory inside of Discord.
Set up channels for different agents:

1. Research Agent (#research): Every morning at 8 AM, research top trending
   stories, competitor content, and what's performing well on social media
   in my niche. Post the top 5 content opportunities with sources.

2. Writing Agent (#scripts): Take the best idea from the research agent
   and write a full script/thread/newsletter draft. Post it in #scripts.

3. Thumbnail Agent (#thumbnails): Generate AI thumbnails or cover images
   for the content. Post them in #thumbnails.

Have all their work organized in different channels.
Run this pipeline automatically every morning.
```

4. 플랫폼에 맞게 맞춤화하세요:
```text
I focus on X/Twitter threads, not YouTube. Change the writing agent
to produce tweet threads instead of video scripts.
```

## 주요 인사이트

- 힘은 **연결된 에이전트(chained agents)**에 있습니다, 리서치가 작성에, 작성이 썸네일에 이어집니다. 각 단계를 개별적으로 프롬프트할 필요가 없습니다.
- Discord 채널은 각 에이전트의 작업을 별도로 검토하고 "스크립트가 너무 길다" 또는 "AI 뉴스에 더 집중해라" 같은 피드백을 주기 쉽게 만듭니다.
- 이 구조는 트윗, 뉴스레터, LinkedIn 게시물, 팟캐스트 개요, 블로그 글 등 어떤 콘텐츠 형식에도 적용할 수 있습니다.
- Mac Studio에서 Nano Banana 같은 로컬 모델로 이미지 생성을 실행하면 비용을 절감하고 제어권을 확보할 수 있습니다.

## 기반

[Alex Finn의 삶을 바꾸는 OpenClaw 활용 사례 영상](https://www.youtube.com/watch?v=41_TNGDDnfQ)에서 영감을 받았습니다.

## 관련 링크

- [OpenClaw Subagent Docs](https://github.com/openclaw/openclaw)
- [Discord Bot Setup](https://discord.com/developers/docs)
