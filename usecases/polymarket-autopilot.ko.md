# Polymarket 자동파일럿: 자동 페이퍼 트레이딩

예측 시장을 수동으로 모니터링하며 차익거래 기회를 찾고 거래를 실행하는 것은 시간이 많이 들고 지속적인 주의가 필요합니다. 실제 자본을 위험에 빠뜨리지 않고 거래 전략을 테스트하고 개선하고 싶습니다.

이 워크플로우는 맞춤형 전략으로 Polymarket에서 페이퍼 트레이딩을 자동화합니다:

• API를 통해 시장 데이터(가격, 거래량, 스프레드)를 모니터링합니다
• TAIL(추세 추종) 및 BONDING(역행) 전략을 사용해 페이퍼 거래를 시뮬레이션합니다
• 포트폴리오 성과, P&L, 승률을 추적합니다
• 거래 로그와 인사이트를 포함한 일간 요약을 Discord에 전달합니다
• 패턴에서 학습합니다: 백테스트 결과에 따라 전략 매개변수를 조정합니다

## 문제점

예측 시장은 빠르게 움직입니다. 수동 거래는 기회를 놓치고 감정적 의사결정을 초래하며 무엇이 효과적인지 추적하기 어렵습니다. 실제 돈으로 전략을 테스트하면 시장 행동을 이해하기 전에 손실을 볼 위험이 있습니다.

## 기능

자동파일럿은 Polymarket을 지속적으로 스캔하여 기회를 찾고 구성 가능한 전략으로 거래를 시뮬레이션하며 분석을 위해 모든 것을 기록합니다. 당신은 밤새 "거래한" 내용과 무엇이 효과적이었는지, 무엇이 그렇지 않았는지 요약을 받아봅니다.

예시 전략:
- **TAIL**: 거래량이 급증하고 모멘텀이 분명할 때 추세를 따릅니다
- **BONDING**: 뉴스로 인해 과잉 반응이 있을 때 역행 포지션을 취합니다
- **SPREAD**: 차익거래 가능성이 있는 잘못 가격된 시장을 식별합니다

## 필요한 기술

- `web_search` 또는 `web_fetch`(Polymarket API 데이터용)
- 거래 로그와 포트폴리오 추적을 위한 `postgres` 또는 SQLite
- 일간 리포트를 위한 Discord 통합
- 지속 모니터링을 위한 cron job
- 병렬 시장 분석을 위한 서브에이전트 생성

## 설정 방법

1. 페이퍼 트레이딩용 데이터베이스 설정:
```sql
CREATE TABLE paper_trades (
  id SERIAL PRIMARY KEY,
  market_id TEXT,
  market_name TEXT,
  strategy TEXT,
  direction TEXT,
  entry_price DECIMAL,
  exit_price DECIMAL,
  quantity DECIMAL,
  pnl DECIMAL,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE portfolio (
  id SERIAL PRIMARY KEY,
  total_value DECIMAL,
  cash DECIMAL,
  positions JSONB,
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

2. 업데이트를 위한 Discord 채널 생성(예: #polymarket-autopilot).

3. OpenClaw에 다음을 프롬프트:
```text
당신은 Polymarket 페이퍼 트레이딩 자동파일럿입니다. 계속 실행하세요(15분마다 cron):

1. Polymarket API에서 현재 시장 데이터 가져오기
2. 다음 전략을 사용해 기회 분석:
   - TAIL: 강한 추세(확률 >60% + 거래량 급증) 따르기
   - BONDING: 뉴스로 인한 과잉 반응에 대한 역행 플레이(갑작스러운 10% 이상 하락)
   - SPREAD: YES+NO > 1.05인 경우 차익거래
3. 데이터베이스에 페이퍼 거래 실행(초기 자본: $10,000)
4. 포트폴리오 상태 추적 및 포지션 업데이트

매일 아침 8시에 Discord #polymarket-autopilot에 요약 게시:
- 어제의 거래(진입/청산 가격, P&L)
- 현재 포트폴리오 가치와 오픈 포지션
- 승률 및 전략 성과
- 시장 인사이트 및 권장 사항

거래량이 많은 기간에는 다수의 시장을 병렬로 분석하기 위해 서브에이전트를 사용하세요.

절대 실제 돈을 사용하지 마세요. 이는 페이퍼 트레이딩 전용입니다.
```

4. 성과를 기반으로 전략을 반복 개선하세요. 임계값을 조정하고, 새 전략을 추가하고, 과거 데이터를 백테스트하세요.

## 관련 링크

- [Polymarket API](https://docs.polymarket.com/)
- [페이퍼 트레이딩 모범 사례](https://www.investopedia.com/articles/trading/11/paper-trading.asp)
