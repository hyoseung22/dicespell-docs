# DiceSpell `Run Table` UI 리디자인 Summary

## 3줄 요약
- 이번 패스의 핵심은 새 시스템 추가가 아니라, 현재 빌드를 `한 런의 보드`처럼 읽히게 재배치한 것이다.
- 읽어야 할 surface는 5개다: `Choose Your Tome`, `Run Atlas`, `Battle Table`, `Run Curation`, `Overrun Oath`.
- 자세한 계약은 `docs/dicespell_game_flow_surface_handoff_packet_kr.md`, 방향 설명은 `docs/dicespell_game_flow_redesign_packet_kr.md`, 시각 샘플은 `docs/prototypes/dicespell_game_flow_redesign_board.html`에 있다.

## 한 줄 목표
현재 DiceSpell UI를 `책 선택 -> 경로 읽기 -> 전투 stakes -> 보상 환전 -> 다음 권 문턱`이 이어지는 런 중심 화면 언어로 고정한다.

## 왜 이 문서가 필요한가
긴 packet을 다 읽기 전에, 각 오너가 `이번 리디자인이 정확히 무엇을 바꿨고 무엇은 아직 안 바꾸는지`를 3~5분 안에 파악하기 위한 companion이다.

---

## 지금 바뀐 것

### 1) Library = `Choose Your Tome`
- 메뉴보다 `이번 런의 성격을 고르는 입구`처럼 읽히게 바뀌었다.
- 책마다 forecast, 테마 축, 초반 운영이 더 먼저 보인다.

### 2) Right Panel = `Run Atlas`
- 단순 페이지 목록보다 `현재 위치 / 다음 상점 / 다음 보스 / 현재 압력`이 먼저 읽힌다.
- route board가 main, nearby page list와 logs는 support다.

### 3) Battle = `Battle Table / Spell Arena`
- 전투는 계산 HUD보다 `이번 페이지 stakes가 걸린 무대`처럼 읽히게 정리됐다.
- 상단 stakes card가 현재 pressure와 다음 문턱을 같이 보여 준다.

### 4) Reward / Shop = `Run Curation`
- 보상과 상점이 모두 `다음 승부처 준비`라는 같은 문법 안에서 읽힌다.
- 차이는 유지하되, 둘 다 `환전`의 자리처럼 느껴지게 한다.

### 5) Ending = `Overrun Oath`
- 엔딩은 `정산창`보다 `이번 권을 봉인하고 다음 권 문턱을 마주하는 순간`처럼 읽히게 바뀌었다.
- 결과와 carry-forward 제안이 같은 화면 안에서 역할 분리된다.

---

## 이번 패스가 아직 안 하는 일
- phase 구조를 새로 만들지 않는다.
- 세이브/로드 계약을 바꾸지 않는다.
- `overrunEvent` scene packet을 대체하지 않는다.
- 첫 런 온보딩 primer packet을 대체하지 않는다.

즉, **메카닉 redesign이 아니라 읽힘 redesign**이다.

---

## 오너별 바로 볼 포인트

| 역할 | 지금 봐야 할 것 | 특히 주의할 것 |
| --- | --- | --- |
| 프로그래머 | surface별 helper / render anchor | view helper가 data truth를 새로 만들지 않게 유지 |
| UI 오너 | 5개 surface family의 위계 차이 | 모든 panel을 같은 glossy card로 평준화하지 않기 |
| 아트 오너 | route node / stakes plaque / oath banner 우선 자산 | 세계관 디테일 확대보다 surface 차이 우선 |
| QA / PM | 5초 안에 화면 역할이 읽히는지 | 예뻐짐보다 `판의 문맥`이 읽히는지 확인 |

---

## 지금 바로 넘길 수 있는 handoff 문장
- `Run Table 패스는 phase와 세이브를 건드리지 않고, library/atlas/battle/curation/ending 5면의 읽힘을 런 보드형으로 재배치하는 데 목적이 있다.`
- `다음 refinement는 새 메카닉 추가가 아니라 surface family 분리, visual token 보강, DOM/QA anchor 정리 쪽이 우선이다.`

---

## 가장 중요한 리스크 4개
1. reward/shop/ending이 모두 카드 family를 쓰다 보니 다시 비슷하게 보일 수 있다.
2. ending 강화가 overrunEvent scene flavor를 침범할 수 있다.
3. library hero가 온보딩 primer 역할까지 먹어버릴 수 있다.
4. route board / stakes / oath의 차이가 아직 CSS 중심이라 아트 handoff에서 약해질 수 있다.

---

## 바로 다음 액션
1. packet + summary + prototype를 포털에서 바로 찾게 연결
2. route board / stakes / oath용 DOM hook 정리
3. 1440p density review 1회
4. 아트는 route node / stakes plaque / oath banner부터 자산화
5. QA는 `첫 5초 역할 읽힘` 체크로 수동 review 시작
