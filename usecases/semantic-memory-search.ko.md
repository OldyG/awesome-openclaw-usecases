# 시맨틱 메모리 검색

OpenClaw의 내장 메모리 시스템은 모든 것을 마크다운 파일로 저장합니다 — 하지만 메모리가 몇 주, 몇 달 동안 쌓이면 지난 화요일에 내린 결정을 찾는 것이 불가능해집니다. 검색 기능은 없이 파일을 스크롤하는 수밖에 없습니다.

이 사용 사례는 [memsearch](https://github.com/zilliztech/memsearch)를 사용해 OpenClaw의 기존 마크다운 메모리 파일 위에 **벡터 기반 시맨틱 검색**을 추가합니다. 그래서 키워드가 아니라 의미로 과거의 어떤 메모든 즉시 찾을 수 있습니다.

## 기능

- 한 명령으로 OpenClaw의 모든 마크다운 메모리 파일을 벡터 데이터베이스(Milvus)로 색인합니다
- 의미로 검색: "우리가 어떤 캐싱 솔루션을 선택했지?"라는 질문은 "caching"이라는 단어가 없어도 관련 메모리를 찾아냅니다
- RRF 재순위를 사용하는 하이브리드 검색(밀집 벡터 + BM25 전체 텍스트)으로 최상의 결과를 제공합니다
- SHA-256 콘텐츠 해싱 덕분에 변경되지 않은 파일은 다시 임베딩되지 않습니다 — 불필요한 API 호출이 없습니다
- 파일 감시자가 메모리 파일이 변경될 때 자동으로 재색인하므로 인덱스가 항상 최신 상태입니다
- OpenAI, Google, Voyage, Ollama 또는 완전 로컬(API 키 불필요) 등 어떤 임베딩 제공자와도 작동합니다

## 문제점

OpenClaw의 메모리는 일반 마크다운 파일로 저장됩니다. 이는 이식성과 사람의 읽기 용이성에 좋지만 검색 기능이 없습니다. 메모가 커지면 파일을 grep으로 뒤져야 하거나(키워드 전용, 의미적 일치를 놓칩니다) 전체 파일을 컨텍스트로 로드해야 합니다(관련 없는 내용에 토큰을 낭비). "X에 대해 내가 무엇을 결정했지?"라고 묻고 정확한 관련 부분을 얻을 방법이 필요합니다.

## 필요한 기술

- OpenClaw 스킬 불필요 — memsearch는 독립 실행형 Python CLI/라이브러리입니다
- Python 3.10+ 및 pip 또는 uv

## 설정 방법

1. memsearch 설치:
```bash
pip install memsearch
```

2. 대화형 구성 마법사 실행:
```bash
memsearch config init
```

3. OpenClaw 메모리 디렉토리 색인화:
```bash
memsearch index ~/path/to/your/memory/
```

4. 의미로 메모리 검색:
```bash
memsearch search "what caching solution did we pick?"
```

5. 실시간 동기화를 위해 파일 감시자 시작 — 파일 변경 시 자동으로 색인합니다:
```bash
memsearch watch ~/path/to/your/memory/
```

6. 완전 로컬 설정(API 키 불필요)을 위해 로컬 임베딩 제공자 설치:
```bash
pip install "memsearch[local]"
memsearch config set embedding.provider local
memsearch index ~/path/to/your/memory/
```

## 주요 인사이트

- **마크다운이 진실의 원천입니다.** 벡터 인덱스는 파생된 캐시일 뿐입니다 — `memsearch index`로 언제든지 재생성할 수 있습니다. 메모리 파일은 결코 변경되지 않습니다.
- **스마트 중복 제거는 비용을 절감합니다.** 각 청크는 SHA-256 콘텐츠 해시로 식별됩니다. `index`를 다시 실행하면 새롭거나 변경된 콘텐츠만 임베딩하므로 자주 실행해도 임베딩 API 호출을 낭비하지 않습니다.
- **하이브리드 검색은 순수 벡터 검색보다 낫습니다.** 의미 유사도(밀집 벡터)와 키워드 매칭(BM25)을 Reciprocal Rank Fusion으로 결합하면 의미 기반과 정확한 일치 쿼리 모두를 포착합니다.

## 관련 링크

- [memsearch GitHub](https://github.com/zilliztech/memsearch) — 이 사용 사례를 지원하는 라이브러리
- [memsearch 문서](https://zilliztech.github.io/memsearch/) — 전체 CLI 참조, Python API, 아키텍처
- [OpenClaw](https://github.com/openclaw/openclaw) — memsearch의 영감을 준 메모리 아키텍처
- [Milvus](https://milvus.io/) — 벡터 데이터베이스 백엔드
