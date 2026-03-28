# DiceSpell `overrunEvent` 컷오버 게이트 요약

## 3줄 요약
- 이 문서는 `overrunEvent` A-track과 첫 런 온보딩 B-track 사이에서 실제 구현자가 **지금 시작해도 되는가 / 여기서 멈춰야 하는가 / 다음 단계를 열어도 되는가**를 3~5분 안에 판단하게 만드는 readable companion이다.
- 핵심은 새 방향을 더하는 것이 아니라, 이미 있는 문서들을 **착수 게이트 / stop 게이트 / 인계 게이트**로 다시 묶는 것이다.
- Commit 2가 `runtime-ready closing green`으로 닫히기 전에는 B-track을 열지 않는다.

**한 줄 목표:** `Commit 1 floor -> Commit 2 closing -> B-1/B-4`를 단계별 go/no-go 언어로 고정해 범위 부풀리기와 과장된 green 기록을 막는다.

## 누가 먼저 봐야 하나
- **프로그래머**: 이번 턴이 어디서 시작되고 어디서 멈춰야 하는지 빨리 확인할 때
- **UI/아트**: 지금이 대기/sanity 턴인지 closing 턴인지 구분할 때
- **QA/PM**: 어떤 증거로 다음 단계 green을 허용해야 하는지 확인할 때

## 첫 화면 결론
- **Commit 1**은 `S1/S2/S3/S7/S8/S9/S10/S11`만 연다.
- **Commit 2**는 `S4/S5/S6/S10/S12`만 연다.
- **B-track**은 Commit 2 closing green 뒤에만 `B-1 -> B-2 -> B-3 -> B-4` 순서로 연다.
- 아래 신호가 나오면 멈추고 문서를 먼저 다시 본다.
  - Commit 1인데 reward apply/page advance가 열림
  - Commit 2인데 onboarding primer를 같이 열려 함
  - B-track인데 surface 여러 개를 한 번에 묶으려 함
  - tracker/run log에 `floor`, `closing`, `B-1~B-4`를 섞어 씀

## 단계별 게이트 표
| 단계 | 시작 전에 확인할 것 | 여기서 닫아야 하는 것 | 여기서 멈춰야 하는 신호 | 다음 단계 개방 조건 |
| --- | --- | --- | --- | --- |
| Commit 1 | `runway -> runtime patch map -> smoke migration -> fixture ledger` 선독 | enter wrapper, canonical snapshot, normalize/recovery, non-change evidence | reward apply, page advance, cleanup, onboarding helper | `floor green` 문장 + S번호 있는 smoke/상태 diff |
| Commit 2 | Commit 1 `floor green` evidence 존재 | scene card, confirm resolve, `resolved` guard, old baseline 제거, S12 sanity | onboarding primer 동시 착수, 새 reward 타입, 전역 카드 리뉴얼 | `runtime-ready closing green` 문장 + full smoke + DOM/시각 evidence |
| B-1 | Commit 2 green + boundary packet 재확인 | library primer only | battle/reward/shop/ending 동시 착수 | `library primer green` |
| B-2 | B-1 green | battle primer only | reward/shop/ending 선착수 | `battle primer green` |
| B-3 | B-2 green | reward/shop primer only | ending 동시 착수 | `reward/shop primer green` |
| B-4 | B-3 green + boundary packet 재확인 | ending primer only | overrun flavor 흡수, 전체 polish | `ending primer green` |

## Commit 1 — floor 게이트
### 왜 존재하나
중간 상태를 안전하게 만들기 위한 단계다. `overrunEvent`로 들어가고, canonical snapshot을 저장하고, recovery를 green으로 만든다.

### 여기서 허용되는 것
- enter wrapper
- canonical snapshot field set 저장
- normalize / recovery
- `none` fallback 수렴
- `resolved` 안전 읽기 바닥

### 여기서 금지되는 것
- reward apply
- `currentPage` 전진
- cleanup
- scene card polish
- onboarding helper/state 추가

### 종료 문장
> 이번 턴은 `overrunEvent` floor green이다. `S1/S2/S3/S7/S8/S9/S10/S11`을 같은 증거 묶음으로 닫았고, reward apply와 onboarding은 아직 열지 않았다.

## Commit 2 — closing 게이트
### 왜 존재하나
중간 상태를 실제 장면과 적용 시점으로 닫는 단계다. confirm CTA에서만 정확히 한 번 넘어가야 한다.

### 여기서 허용되는 것
- 장면 카드 위계
- `themeTrophy` / `enemyTrophy` / `none` 3축 시각 sanity
- confirm 시 reward 1회 적용
- `resolved` reload/reclick no-op
- old immediate baseline 제거

### 여기서 금지되는 것
- onboarding primer 동시 착수
- 새 reward 타입 추가
- 전역 카드 시스템 리뉴얼
- ending에 overrun flavor 복제

### 종료 문장
> 이번 턴은 `overrunEvent` runtime-ready closing green이다. `S4/S5/S6/S10/S12`를 같은 증거 묶음으로 닫았고, 다음은 `B-1 library primer`만 연다.

## B-track 공통 게이트
- A-track Commit 2 green 전에는 시작하지 않는다.
- 각 단계는 `surface 1개 = commit 1개 = green 문장 1개`다.
- `library+battle`, `reward/shop+ending`처럼 두 surface를 한 번에 닫으려 하면 stop한다.
- B-4에서도 `ending`은 결과/CTA 의미만 맡고, `overrunEvent` flavor를 흡수하지 않는다.

## 역할별 승인 기준
| 역할 | 다음 단계 green을 허용하려면 확인해야 하는 것 |
| --- | --- |
| 프로그래머 | 해당 단계 문서 선독 + 단계 픽스처 번호 잠금 + 금지 범위 확인 |
| UI | 이번 턴이 sanity 턴인지 closing 턴인지 명확함 |
| 아트 | token/accent sanity와 신규 자산 범위가 분리돼 있음 |
| QA | 단계명과 S번호가 같이 붙은 evidence bundle |
| PM/기획 | tracker/run log 문장에 단계명과 다음 허용 단계가 함께 적힘 |

## 리스크 & 후속과제
- 문서가 많아질수록 `이 정도면 같이 해도 되겠지`라는 실행 드리프트가 더 커질 수 있다.
- `S10`처럼 양쪽 커밋에 걸친 guard 픽스처는 특히 기록이 흐려지기 쉽다.
- B-track은 준비됐어도 A-track closing 전에는 열지 않는 discipline이 핵심 리스크다.
- 다음 슬롯의 최우선은 여전히 새 문서 추가가 아니라 실제 A-track 코드 컷오버다. 다만 착수 전에는 이번 게이트 문서부터 열어 go/no-go를 잠가야 한다.

## 바로 다음 액션
1. `runway -> runtime patch map -> smoke migration packet -> runtime fixture ledger -> cutover gate packet` 순서로 착수 게이트를 잠근다.
2. Commit 1은 floor evidence만 남긴다.
3. Commit 2는 closing evidence만 남긴다.
4. 그 뒤에만 `B-1 library primer`를 연다.
