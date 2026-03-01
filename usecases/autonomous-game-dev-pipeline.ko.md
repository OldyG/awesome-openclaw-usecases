# 자율 교육용 게임 개발 파이프라인

## 문제점

**시작 이야기:** 한때 LANero였던 아버지는 딸들 Susana(3세)와 곧 태어날 Julieta를 위해 안전하고 광고 없는 고품질 게임 포털을 만들고자 했습니다. 기존 사이트들은 스팸, 공격적인 광고, 기만적인 버튼(다크 패턴)으로 넘쳐나 어린아이를 좌절하게 했습니다.

**도전 과제:** "깨끗하고, 빠르고, 단순한" 포털을 만드는 것은 쉬운 편이었습니다. 진짜 문제는 특정 발달 단계(0-15세)에 맞춘 40개 이상의 교육용 게임을 개발팀 없이 채워 넣는 것이었습니다. 개인 개발자가 수동으로 개발하기엔 너무 느렸고, 수십 개의 게임 간 일관성을 유지하는 것은 악몽에 가까웠습니다.

## 기능

이 사용 사례는 게임의 생성과 유지보수 전체 수명주기를 자율적으로 관리하는 "Game Developer Agent"를 정의합니다. 워크플로우는 **"Bugs First"** 정책을 적용해 에이전트가 새 기능을 구현하기 전에 보고된 버그를 먼저 확인하고 해결하도록 요구합니다.

**효율성:** 이 파이프라인은 **7분마다 1개 게임 또는 버그 수정**을 생성할 수 있습니다. 에이전트는 41개 이상의 예정된 게임 백로그를 끊임없이 반복하며 새 콘텐츠 생성과 이전 주기에서 발견된 문제 수정 사이를 번갈아 진행합니다.

작업 흐름이 명확할 때 에이전트는 다음을 수행합니다:
1. **선택**: "Round Robin" 전략에 따라 연령대별 균형을 맞추며 `development-queue.md`의 다음 게임을 식별합니다.
2. **구현**: `game-design-rules.md`를 엄격히 따르며 게임용 HTML5/CSS3/JS 코드를 작성합니다(프레임워크 사용 금지, 모바일 우선, 오프라인 지원).
3. **등록**: 중앙 레지스트리(`games-list.json`)에 게임 메타데이터를 자동으로 추가합니다.
4. **문서화**: `CHANGELOG.md`와 `master-game-plan.md` 상태를 업데이트합니다.
5. **배포**: Git 워크플로우를 처리합니다: master를 가져오고, 기능 브랜치를 만들고, 컨벤셔널 커밋으로 커밋한 뒤 병합합니다.

## 프롬프트

이 워크플로우의 핵심은 에이전트에게 주어지는 **시스템 지침**입니다. 이 프롬프트는 LLM을 프로젝트의 엄격한 구조를 준수하는 규율 있는 개발자로 만듭니다.

*(**참고:** 실제 운영에서 사용되는 프롬프트는 대상 사용자(라틴 아메리카 어린이)와 향후 기여자를 고려해 **스페인어**(`es-419`)로 작성되어 있습니다. 아래 버전은 문서화를 위해 번역한 것입니다.)*

```text
Act as an Expert in Web Game Development and Child UX.
Your goal is to develop the next game in the production queue.

Please read and analyze the following context files before starting:

1.  BUG CONTEXT (Top Priority - CRITICAL):
    @[bugs/]
    (Check this folder. If there are files, YOUR TASK IS TO FIX **ONLY THE FIRST FILE** (in alphabetical order). Ignore the rest of the bugs and the game queue for now).

2.  QUEUE CONTEXT (Which game is next):
    @[development-queue.md]
    (Identify the game marked as [NEXT] in the "Next Games" section. ONLY if there are no bugs).

3.  DESIGN RULES (Technical Standards):
    @[game-design-rules.md]
    (Strictly follow these rules: Pure HTML/CSS/JS, folder structure, mobile responsiveness)

4.  GAME SPECIFICATIONS (Mechanics and Assets):
    (Identify the corresponding file in games-backlog/ based on the game ID)

5.  CENTRAL REGISTRY (Integration):
    @[public/js/games-list.json]
    (File where you MUST register the new game so it appears on the home page)

TASK:
0. **BUGS FIRST!**: If the `bugs/` folder has content, your only priority is to fix **the first bug in alphabetical order**. Create a `fix/...` branch, resolve **that** bug, update status, and merge. **Do not attempt to fix multiple bugs at once.**
   - IF THERE ARE NO BUGS, proceed with the next game:

1. **Synchronization**: `git fetch && git pull origin master` (CRITICAL).
2. Create a new branch: `git checkout -b feature/[game-id]`.
3. Create the folder and files in 'public/games/[game-id]/'.
4. Implement logic and design according to the backlog and design rules.
5. Register the game in 'games-list.json' (CRITICAL).
6. When finished:
    - Update `CHANGELOG.md` bumping the version.
    - Update `master-game-plan.md` and `development-queue.md`.
    - Document changes: `git commit -m "feat: add [game-id]"`.
7. **Delivery**:
    - Push: `git push origin feature/[game-id]`.
    - Request merge to master.
    - Once in master, push changes (`git push origin master`).
```

## 필요한 스킬

- **Git**: 브랜치, 커밋, 병합 관리를 위해 필요합니다.

## 관련 링크

- [Project Origin Story (LinkedIn)](https://www.linkedin.com/feed/update/urn:li:activity:7429739200301772800/) - OpenClaw를 구성한 후 이 프로젝트가 어떻게 시작되었는지
- [El Bebe Games Repository](https://github.com/duberblockito/elbebe/tree/master) - 소스 코드
- [El Bebe Games Live Site](https://elbebe.co/) - 이 파이프라인의 결과물
- [HTML5 Game Development Best Practices](https://developer.mozilla.org/en-US/docs/Games)
