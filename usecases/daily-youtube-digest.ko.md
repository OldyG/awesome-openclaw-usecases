#RQ|# 일간 YouTube 요약

좋아하는 YouTube 채널의 새 영상을 개인 맞춤 요약으로 받아보며 하루를 시작하세요. 실제로 팔로우하고 싶은 크리에이터의 콘텐츠를 더 이상 놓치지 않습니다.

## 문제점

YouTube 알림은 신뢰할 수 없습니다. 채널을 구독해도 새 영상이 홈 피드에 나타나지 않습니다. 알림에도 없고, 그냥... 사라집니다. 이것이 당신이 그 영상을 보고 싶지 않다는 뜻은 아닙니다. YouTube 알고리즘이 묻어버린 것입니다.

게다가, 추천 피드를 무한 스크롤하는 대신 큐레이션된 콘텐츠 인사이트로 하루를 시작하는 것이 더 즐겁습니다.

## 기능

- 좋아하는 채널 목록에서 최신 영상을 가져옵니다
- 각 영상의 자막에서 요약 또는 핵심 인사이트를 추출합니다
- 매일 (또는 필요할 때) 요약을 제공합니다

## 필요한 스킬

다음 스킬을 설치하세요: [youtube-full](https://clawhub.ai/therohitdas/youtube-full).

설정은 간단합니다. OpenClaw에 이렇게 말하세요:
```text
"Install the youtube-full skill and set it up for me"
```
또는
```bash
npx clawhub@latest install youtube-full
```

그게 전부입니다. 에이전트가 나머지를 처리합니다 — 계정 생성과 API 키 설정까지 포함하여. 가입 시 **100 무료 크레딧**을 받습니다. 신용카드가 필요 없습니다.

> 참고: 계정을 만든 후, 스킬은 운영체제에 맞는 위치에 API 키를 안전하게 자동 저장하므로 모든 컨텍스트에서 API가 작동합니다.

![youtube-full skill installation](https://pub-15904f15a44a4ea69350737e87660b92.r2.dev/media/1770620159490-e41e7baa.png)

### 왜 yt-dlp 대신 TranscriptAPI.com인가?

| CLI tools (yt-dlp, etc.) | TranscriptAPI |
|--------------------------|---------------|
| Verbose logs flood agent context | Clean JSON responses |
| Doesn't work on GCP/cloud OpenClaw | Works everywhere, fast |
| Gets blocked randomly by YouTube | Powers [YouTubeToTranscript.com](https://youtubetotranscript.com) serving millions. Cached and reliable. |
| Requires binary installation | No binaries, just HTTP |

## 설정 방법

### 옵션 1: 채널 기반 요약

OpenClaw에 요청하세요:
```text
Every morning at 8am, fetch the latest videos from these YouTube channels and give me a digest with key insights from each:

- @TED
- @Fireship
- @ThePrimeTimeagen
- @lexfridman

For each new video (uploaded in the last 24-48 hours):
1. Get the transcript
2. Summarize the main points in 2-3 bullets
3. Include the video title, channel name, and link

If a channel handle doesn't resolve, search for it and find the correct one.
Save my channel list to memory so I can add/remove channels later.
```

### 옵션 2: 키워드 기반 요약

특정 주제에 대한 새 영상을 추적하세요:
```text
Every day, search YouTube for new videos about "OpenClaw" (or "Claude Code", "AI agents", etc).

Maintain a file called seen-videos.txt with video IDs you've already processed.
Only fetch transcripts for videos NOT in that file.
After processing, add the video ID to seen-videos.txt.

For each new video:
1. Get the transcript
2. Give me a 3-bullet summary
3. Note anything relevant to my work

Run this every morning at 9am.
```

이렇게 하면 이미 본 영상의 크레딧을 재사용하지 않도록 방지할 수 있습니다.

## 팁

- `channel/latest`과 `channel/resolve`는 **무료**(0 크레딧)입니다. 새 업로드 확인에는 비용이 들지 않습니다.
- 트랜스크립트, 즉 전사만 영상당 1 크레딧이 소요됩니다.
- 요약 스타일을 다양하게 요청할 수 있습니다: 핵심 요약, 주목할 만한 인용구, 흥미로운 순간의 타임스탬프 등
- 이미 유사한 제품이 있습니다: [Recapio - Daily YouTube Recap](https://recapio.com/features/daily-recaps)
