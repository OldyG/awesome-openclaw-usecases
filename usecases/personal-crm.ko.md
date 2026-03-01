# 개인 CRM, 자동 연락처 발견

누가 언제 누구를 만나 무엇을 논의했는지 수동으로 기록하는 것은 불가능에 가깝습니다. 중요한 후속 조치가 누락되고, 중요한 회의 전에 맥락을 잊어버리곤 합니다.

이 워크플로는 개인 CRM을 자동으로 구축하고 유지합니다:

• 매일 cron 작업이 이메일과 캘린더를 스캔해 새로운 연락처와 상호작용을 찾음
• 연락처를 관계 맥락과 함께 구조화된 데이터베이스에 저장함
• 자연어 질의: "What do I know about [person]?, Who needs follow-up?, When did I last talk to [person]?"
• 일일 회의 준비 브리핑: 매일 회의 전에 CRM과 이메일 기록을 통해 외부 참석자를 조사하고 브리핑을 전달함

## 필요한 기술

- `gog` CLI (Gmail 및 Google Calendar용)
- 맞춤형 CRM 데이터베이스 (SQLite 또는 유사한 것) 또는 사용 가능한 경우 [crm-query](https://clawhub.ai) 스킬 사용
- CRM 질의를 위한 Telegram 토픽

## 설정 방법

1. CRM 데이터베이스 생성:
```sql
CREATE TABLE contacts (
  id INTEGER PRIMARY KEY,
  name TEXT,
  email TEXT,
  first_seen TEXT,
  last_contact TEXT,
  interaction_count INTEGER,
  notes TEXT
);
```
2. Telegram에서 "personal-crm"이라는 토픽을 만드세요.
3. OpenClaw에 다음을 프롬프트하세요:
```text
Run a daily cron job at 6 AM to:
1. Scan my Gmail and Calendar for the past 24 hours
2. Extract new contacts and update existing ones
3. Log interactions (meetings, emails) with timestamps and context

Also, every morning at 7 AM:
1. Check my calendar for today's meetings
2. For each external attendee, search my CRM and email history
3. Deliver a briefing to Telegram with: who they are, when we last spoke, what we discussed, and any follow-up items

When I ask about a contact in the personal-crm topic, search the database and give me everything you know.
```
