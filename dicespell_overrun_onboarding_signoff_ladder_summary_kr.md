# DiceSpell `overrunEvent` / 첫 런 온보딩 사인오프 래더 요약

## 3줄 요약
- 이 문서는 `Commit 1 floor -> Commit 2 closing -> B-1/B-2/B-3/B-4`를 **누가 signoff하고 누가 다음 단계를 여는지** 빠르게 보여 주는 readable companion이다.
- 핵심은 새 설계를 더하는 것이 아니라 `review-ready`, `QA pass`, `PM green`, `next unlock`을 같은 뜻으로 쓰지 못하게 막는 것이다.
- 첫 화면만 읽어도 프로그래머/UI/아트/QA/PM이 `내 제출물`, `내 veto 조건`, `내 green 문장`을 바로 판단해야 한다.

## 한 줄 목표
문서 세트를 `충분히 읽은 상태`에서 한 단계 더 밀어 **단계별 승인 사다리**로 고정한다.

## 왜 필요한가
지금 DiceSpell의 남은 위험은 설계 공백보다 `좋아, evidence는 있는데 정확히 누가 green을 선언하지?`가 owner마다 다르게 해석될 수 있다는 점이다.

- 프로그래머 제출만으로는 다음 단계가 열리지 않는다.
- QA pass만으로도 다음 단계가 열리지 않는다.
- PM green 문장까지 남아야 다음 단계가 열린다.
- surface 둘 이상을 한 번에 green으로 쓰는 순간 B-track 경계가 무너진다.

---

## 첫 화면 결론
- **Commit 1 floor**: 프로그래머 제출 → QA가 non-change line 포함 pass → PM이 `floor green` 기록
- **Commit 2 closing**: 프로그래머 제출 → UI/아트 closing sanity → QA가 `B-1만 열 수 있음` 기록 → PM이 `runtime-ready closing green` 기록
- **B-1~B-4**: 각 surface마다 `프로그래머 제출 -> UI/아트 sanity -> QA unlock -> PM green` 순서 유지
- **금지**: reviewer 메모만 있고 PM green이 없는 상태에서 다음 단계 개방

---

## 단계별 signoff 한눈표

| 단계 | 마지막 green 기록자 | 다음 단계 개방 조건 | 아직 금지 |
| --- | --- | --- | --- |
| Commit 1 floor | PM | `S1/S2/S3/S7/S8/S9/S10/S11` + non-change line + `floor green` | UI/아트 closing, resolve, B-track 개방 |
| Commit 2 closing | PM | `S4/S5/S6/S10/S12` + scene/resolve evidence + `runtime-ready closing green` | B-2~B-4 동시 개방 |
| B-1 library | PM | B1 acceptance/recovery + `library primer green` | B-2 동시 구현 |
| B-2 battle | PM | B2 acceptance/recovery + `battle primer green` | B-3 동시 구현 |
| B-3 reward/shop | PM | B3 acceptance/recovery + `reward/shop primer green` | B-4 동시 구현 |
| B-4 ending | PM | B4 acceptance/recovery + boundary evidence + `ending primer green` | overrun flavor를 ending helper가 흡수하는 것 |

---

## 역할별 한 줄 handoff
- **프로그래머**: 코드 diff는 green 선언이 아니라 제출물이다. 항상 `아직 금지된 범위`를 같이 적는다.
- **UI**: Commit 1은 검토 메모만, 실제 closing signoff는 Commit 2부터다.
- **아트**: 새 자산 유무보다 boundary drift 여부가 signoff 핵심이다.
- **QA**: pass 문장에는 반드시 `다음에 열 수 있는 단계 하나`를 같이 적는다.
- **PM**: PM green이 없으면 다음 단계는 열리지 않는다.

---

## 기록 문장 규칙
- QA pass가 없으면 PM green이 없다.
- PM green이 없으면 next unlock이 없다.
- run log는 append-only다.
- `오버런 완료`, `온보딩 진행`, `거의 완료` 같은 큰 문장은 금지한다.

### 복붙용 PM green 문장
- `이번 턴은 overrunEvent Commit 1 floor green이다.`
- `이번 턴은 overrunEvent Commit 2 runtime-ready closing green이다.`
- `이번 턴은 B-1 library primer green이다.`
- `이번 턴은 B-2 battle primer green이다.`
- `이번 턴은 B-3 reward/shop primer green이다.`
- `이번 턴은 B-4 ending primer green이다.`

---

## 리스크 & 후속과제
- reviewer 메모와 실제 signoff가 같은 말로 남으면 half-cutover가 다시 완료처럼 보인다.
- Commit 1에서 UI/아트 sanity 메모를 closing 승인처럼 쓰면 Commit 2 의미가 사라진다.
- B-track을 `온보딩 진행` 한 문장으로 쓰면 surface 경계가 무너진다.
- PM green 없이 다음 단계가 열리면 tracker/run log/포털이 서로 다른 baseline을 가리킨다.

---

## 즉시 다음 액션
1. `cutover readiness board -> execution reporting packet -> signoff ladder` 순서로 읽고 signoff 순서를 먼저 잠근다.
2. Commit 1은 QA unlock 뒤 PM green으로만 닫는다.
3. Commit 2는 UI/아트 sanity와 QA unlock 뒤 PM green으로만 닫는다.
4. B-track은 surface 하나씩만 연다.

---

## 바로 가기
- `docs/dicespell_overrun_onboarding_signoff_ladder_kr.md`
- `docs/dicespell_overrun_onboarding_cutover_readiness_board_kr.md`
- `docs/dicespell_overrun_onboarding_execution_reporting_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
- `docs/dicespell_first_run_onboarding_handoff_scorecard_kr.md`
