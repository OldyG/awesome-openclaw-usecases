# Todoist 작업 관리자: 에이전트 작업 가시성
장기 실행형 에이전트 워크플로우의 투명성을 극대화하기 위해 내부 추론과 진행 로그를 Todoist에 직접 동기화합니다.

## 문제점
에이전트가 복잡하고 여러 단계인 작업(예: 풀스택 앱 빌드, 심층 조사)을 수행할 때 사용자는 에이전트가 지금 무엇을 하고 있는지, 어떤 단계가 완료되었는지, 어디에서 막혔는지 파악하기 어렵습니다. 백그라운드 작업의 채팅 로그를 수동으로 확인하는 건 번거롭습니다.

## 기능
이 사용 사례는 `todoist-task-manager` 스킬을 사용하여 다음을 수행합니다:
1.  **상태 시각화**: `🟡 In Progress` 또는 `🟠 Waiting` 같은 특정 섹션에 작업을 생성합니다.
2.  **추론 외부화**: 에이전트의 내부 "Plan"을 작업 설명에 게시합니다.
3.  **로그 스트리밍**: 하위 단계 완료를 실시간으로 작업 댓글로 추가합니다.
4.  **자동 조정**: 하트비트 스크립트가 정체된 작업을 확인하고 사용자에게 알립니다.

## 필요한 기술
사전 제작된 스킬이 필요하지 않습니다. 아래의 **설정 가이드**에 설명된 bash 스크립트를 OpenClaw에 생성하도록 요청하면 됩니다. OpenClaw은 자체 파일시스템을 관리하고 셸 명령을 실행할 수 있어 요청 시 사실상 스킬을 "구축"합니다.

## 상세 설정 가이드

### 1. Todoist 구성
프로젝트(예: "OpenClaw Workspace")를 만들고 ID를 얻습니다. 상태별 섹션을 생성합니다:
- `🟡 In Progress`
- `🟠 Waiting`
- `🟢 Done`

### 2. 구현: "에이전트가 만든" 스킬
스킬을 설치하는 대신 OpenClaw에게 다음 스크립트를 생성하도록 요청할 수 있습니다. 각 스크립트는 Todoist API와의 통신의 다른 부분을 처리합니다.

**`scripts/todoist_api.sh`** (핵심 래퍼):
```bash
#!/bin/bash
# Usage: ./todoist_api.sh <endpoint> <method> [data_json]
ENDPOINT=$1
METHOD=$2
DATA=$3
TOKEN="YOUR_TODOIST_API_TOKEN"

if [ -z "$DATA" ]; then
  curl -s -X "$METHOD" "https://api.todoist.com/rest/v2/$ENDPOINT" \
    -H "Authorization: Bearer $TOKEN"
else
  curl -s -X "$METHOD" "https://api.todoist.com/rest/v2/$ENDPOINT" \
    -H "Authorization: Bearer $TOKEN" \
    -H "Content-Type: application/json" \
    -d "$DATA"
fi
```

**`scripts/sync_task.sh`** (작업 및 상태 관리):
```bash
#!/bin/bash
# Usage: ./sync_task.sh <task_content> <status> [task_id] [description] [labels_json_array]
CONTENT=$1
STATUS=$2
TASK_ID=$3
DESCRIPTION=$4
LABELS=$5
PROJECT_ID="YOUR_PROJECT_ID"

case $STATUS in
  "In Progress") SECTION_ID="SECTION_ID_PROGRESS" ;;
  "Waiting")     SECTION_ID="SECTION_ID_WAITING" ;;
  "Done")        SECTION_ID="SECTION_ID_DONE" ;;
  *)             SECTION_ID="" ;;
esac

PAYLOAD="{\"content\": \"$CONTENT\""
[ -n "$SECTION_ID" ] && PAYLOAD="$PAYLOAD, \"section_id\": \"$SECTION_ID\""
[ -n "$PROJECT_ID" ] && [ -z "$TASK_ID" ] && PAYLOAD="$PAYLOAD, \"project_id\": \"$PROJECT_ID\""
if [ -n "$DESCRIPTION" ]; then
  ESC_DESC=$(echo "$DESCRIPTION" | sed ':a;N;$!ba;s/\n/\\n/g' | sed 's/"/\\"/g')
  PAYLOAD="$PAYLOAD, \"description\": \"$ESC_DESC\""
fi
[ -n "$LABELS" ] && PAYLOAD="$PAYLOAD, \"labels\": $LABELS"
PAYLOAD="$PAYLOAD}"

if [ -n "$TASK_ID" ]; then
  ./scripts/todoist_api.sh "tasks/$TASK_ID" POST "$PAYLOAD"
else
  ./scripts/todoist_api.sh "tasks" POST "$PAYLOAD"
fi
```

**`scripts/add_comment.sh`** (진행 로그 추가):
```bash
#!/bin/bash
# Usage: ./add_comment.sh <task_id> <comment_text>
TASK_ID=$1
TEXT=$2
ESC_TEXT=$(echo "$TEXT" | sed ':a;N;$!ba;s/\n/\\n/g' | sed 's/"/\\"/g')
PAYLOAD="{\"task_id\": \"$TASK_ID\", \"content\": \"$ESC_TEXT\"}"
./scripts/todoist_api.sh "comments" POST "$PAYLOAD"
```

### 3. 사용법 프롬프트
다음 프롬프트를 에이전트에게 주면 가시성 시스템을 **설정**하고 **사용할** 수 있습니다:

```text
I want you to build a Todoist-based task visibility system for your own runs. 

First, create three bash scripts in a 'scripts/' folder: 
1. todoist_api.sh (a curl wrapper for Todoist REST API)
2. sync_task.sh (to create or update tasks with specific section_ids for In Progress, Waiting, and Done)
3. add_comment.sh (to post progress logs as comments)

Use these variables for the setup:
- Token: [Your Todoist API Token]
- Project ID: [Your Project ID]
- Section IDs: [In Progress ID, Waiting ID, Done ID]

Once created, for every complex task I give you:
1. Create a task in 'In Progress' with your full PLAN in the description.
2. For every sub-step completion, call add_comment.sh with a log of what you did.
3. Move the task to 'Done' when finished.
```

## 관련 링크
- [Todoist REST API Documentation](https://developer.todoist.com/rest/v2/)


(End of file - total 118 lines)
