# 받은편지함 정리

뉴스레터는 받은편지함을 어떤 것보다도 빠르게 채울 수 있습니다. 열어보지도 않고 쌓이는 경우가 많습니다.

## 필요한 스킬
[Gmail OAuth Setup](https://clawhub.ai/kai-jar/gmail-oauth).

## 설정 방법
1. [선택사항] OpenClaw 전용 Gmail을 새로 만드세요.
2. [선택사항] 기존 이메일의 뉴스레터 구독을 모두 해지하고 OpenClaw 이메일로 다시 구독하세요.
3. 스킬을 설치하고 정상 작동하는지 확인하세요.
4. OpenClaw에 지시하세요:
```txt
I want you to run a cron job everyday at 8 p.m. to read all the newsletter emails of the past 24 hours and give me a digest of the most important bits along with links to read more. Then ask for my feedback on whether you picked good bits, and update your memory based on my preferences for better digests in the future jobs.
```
