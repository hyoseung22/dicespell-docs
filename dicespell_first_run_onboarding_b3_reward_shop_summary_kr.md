# DiceSpell 첫 런 온보딩 B-3 Reward / Shop Primer 요약

## 빠른 안내

### 3줄 요약
- B-3는 온보딩 전체가 아니라 **첫 reward 1면 + 첫 shop 1면 badge-primer**를 실제 구현 가능한 commit 범위로 줄인 문서다.
- reward는 `즉시 선택`, shop은 `장기 투자`로 읽혀야 하며, 둘 다 CTA와 카드 그리드를 가리지 않는 얇은 strip 밀도를 유지해야 한다.
- boss draft 공존, `seenRewardPrimer` / `seenShopPrimer` 저장, reward-vs-shop 차등이 이번 단계의 핵심 검수선이다.

**한 줄 목표:** 첫 reward와 첫 shop에서 플레이어가 두 화면의 역할 차이를 바로 읽게 만들되, 선택/구매 흐름은 전혀 막지 않는다.

## 왜 이 문서를 먼저 읽나

- 기존 온보딩 문서는 방향, 화면 위치, exact content를 충분히 닫았지만, 실제 구현 직전 `B-3 reward/shop 첫 커밋에서 어디까지가 범위인가`는 별도로 좁혀 둘 필요가 있었다.
- reward와 shop은 둘 다 작은 안내를 붙이기 쉬운 surface라서, 오히려 같은 strip / 같은 문장 / 같은 색으로 뭉개질 위험이 크다.
- 이 요약 문서는 프로그래머/UI/아트/QA가 B-3 범위를 3~5분 안에 맞춰 읽도록 만든 readable companion이다.

## 이번 B-3에서 실제로 닫는 것

- `renderReward(summary)` 상단의 `reward.first` primer 1면
- `renderShop()` 상단의 `shop.first` primer 1면
- `seenRewardPrimer`, `seenShopPrimer` 2필드
- reward + boss draft 동시 노출 시 primer 1회 유지 규칙
- reward는 warm, shop은 cool로 읽히는 최소 시각 차등
- CTA / 카드 선택 / 구매 흐름 비차단

## 이번 B-3에서 일부러 하지 않는 것

- ending primer 구현
- reward/shop 상세 튜토리얼
- 보상/상점 선택 로직 변경
- reward/shop/ending을 한 커밋에 같이 여는 일

## 완료 조건

B-3는 아래가 모두 참이면 닫힌다.

1. 첫 reward에서 `첫 보상` primer가 1회만 뜬다.
2. 첫 shop에서 `첫 상점` primer가 1회만 뜬다.
3. reward는 `즉시 선택`, shop은 `장기 투자`로 실제 체감상 다르게 읽힌다.
4. reward 선택 / boss draft 선택 / shop 구매 / `다음 페이지로` CTA가 primer 때문에 막히지 않는다.
5. `uiGuidance` 일부 키가 누락돼도 해당 primer만 재노출되고 flow는 그대로 산다.
6. 이 커밋 안에 ending 온보딩이나 로직 변경이 섞이지 않는다.

## 역할별 한 줄 handoff

- 프로그래머: `renderReward(summary)` / `renderShop()` slot + seen 2필드 + boss draft 중복 방지까지만.
- UI: reward/shop이 같은 strip 복붙처럼 보이지 않게 accent와 배치 체감을 분리.
- 아트: `primer-reward`, `primer-shop` 최소 token 2세트면 충분.
- QA: 첫 reward, 첫 shop, boss draft 동시 노출, 저장 키 누락 recovery를 꼭 본다.

## 리스크 & 후속과제

- 가장 큰 리스크는 `둘 다 작은 안내니까 같이 빨리 붙인다`가 아니라, **작아서 더 쉽게 같은 구조로 붙어 버리는 것**.
- 두 번째 리스크는 reward에서 boss draft가 함께 열릴 때 primer가 중복되거나, shop에서 기존 body-copy와 primer가 같은 말을 반복하는 것.
- 세 번째 리스크는 B-3을 열면서 ending primer까지 같이 붙여 범위가 다시 커지는 것.

## 다음 액션

- overrun A-track 실구현이 아직 최우선이면 B-3는 handoff-ready 상태로만 유지한다.
- 온보딩 B-track이 실제로 열리면, 순서는 반드시 `B-1 library -> B-2 battle -> B-3 reward/shop`이다.
- 그 다음 commit-level 문서는 필요 시 `B-4 ending primer packet`으로 분리한다.
