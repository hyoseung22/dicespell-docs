# DiceSpell `overrunEvent` 런타임 픽스처 레저

## 3줄 요약
- 이 문서는 `overrunEvent` A-track 실구현 직전 가장 쉽게 다시 흩어지는 `S1~S12 픽스처`, `Commit 1/2 종료선`, `오너별 제출 증거`를 한 장으로 묶는 **source-of-truth ledger**다.
- 프로그래머는 `어떤 smoke/state/UI 증거가 어느 커밋에 속하는지`, UI/아트/QA는 `이번 턴에 무엇을 봐야 하고 무엇은 아직 보면 안 되는지`를 여기서 바로 확인하면 된다.
- 핵심은 새 설계를 더하는 것이 아니라, 이미 닫힌 smoke migration/state matrix/review/scorecard 문서를 **같은 픽스처 언어**로 다시 정렬해 half-cutover와 과장된 green 기록을 막는 것이다.

**한 줄 목표:** `S1~S12` 픽스처를 `Commit 1 floor`와 `Commit 2 closing`에 정확히 배치하고, 각 픽스처의 증거 묶음을 프로그래머/UI/아트/QA/PM이 같은 문장으로 재사용하게 만든다.

## 왜 이 문서가 필요한가
- 지금 DiceSpell의 `overrunEvent` 문서는 충분하다. 문제는 문서가 없어서가 아니라, 실제 구현/리뷰 턴에서 `S1~S12`가 다시 여러 문서에 흩어져 읽힌다는 점이다.
- `smoke migration packet`은 fixture 정의를, `state matrix`는 non-change line을, `review packet`은 pass/fail 문장을, `scorecard`는 제출 순서를 잡아 준다. 그런데 실구현 직전에는 여전히 `그래서 S4는 Commit 1인가 Commit 2인가`, `UI/아트는 S12만 보면 되는가`, `PM은 어떤 픽스처 green부터 기록해야 하는가`를 다시 머릿속에서 짜맞추게 된다.
- 이 레저는 그 마지막 병목을 줄이기 위해 **픽스처 번호 / 소유 오너 / 증거 묶음 / 허용 커밋 / 다음 handoff**를 한 표로 고정한다.

---

## 이 문서를 먼저 봐야 하는 사람
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 이 문서에서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. Commit 1/2 픽스처 배치표`, `2. 픽스처별 상태/함수 앵커` | `first runtime delivery packet`, `second runtime slice packet` | 이번 커밋에 넣을 smoke/assertion 범위를 다시 발명하지 않는다 |
| UI | `3. UI/DOM evidence 레저`, `4. 오너별 handoff` | `A-3 UI packet`, `ending-overrun boundary packet` | S12와 Commit 2 shell sanity만 본다 |
| 아트 | `3. UI/DOM evidence 레저`, `4. 오너별 handoff` | `content deck`, `A-3 UI packet` | branch signifier drift만 본다 |
| QA | `1. Commit 1/2 픽스처 배치표`, `5. 리뷰 제출 순서` | `second runtime review packet`, `runtime handoff scorecard` | 같은 fixture 번호로 green/fail을 기록한다 |
| PM/기획 | `0. first screen 결론`, `5. 복붙용 기록 문장` | `runtime commit stack packet`, `owner handoff packet` | Commit 1과 Commit 2를 다른 녹색 문장으로 남긴다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `S1~S12 중 이번 커밋에서 닫아야 하는 픽스처는 무엇인가?`
- `같은 픽스처라도 프로그래머/UI/아트/QA/PM이 각각 어떤 증거를 제출해야 하는가?`
- `tracker/run log에는 어느 픽스처 세트가 green이라고 써야 과장되지 않는가?`

### 가장 짧은 답
- **Commit 1**은 `S1, S2, S3, S7, S8, S9, S10, S11`까지만 닫는다. 핵심은 `진입 + canonical snapshot + recovery 수렴`이다.
- **Commit 2**는 `S4, S5, S6, S10, S12`를 닫는다. 핵심은 `scene card + confirm 1회 적용 + old baseline 제거`다.
- **S10**은 양쪽 커밋에 걸친 guard fixture다. Commit 1에서는 `resolved` 상태를 안전하게 읽는 바닥, Commit 2에서는 실제 재실행 금지를 증명하는 closing 증거로 다룬다.

---

## 1. Commit 1 / Commit 2 픽스처 배치표

### 1-1. 한눈에 보는 배치표
| 픽스처 | 이름 | 핵심 질문 | 허용 커밋 | 1차 소유 오너 | 필수 증거 |
| --- | --- | --- | --- | --- | --- |
| S1 | theme trophy enter | 책 전리품 진입이 `phase='overrunEvent'`와 canonical snapshot으로만 닫히는가 | Commit 1 | 프로그래머 | smoke + 상태 diff |
| S2 | enemy trophy enter | 적 전리품 진입도 즉시 적용 없이 같은 구조로 들어오는가 | Commit 1 | 프로그래머 | smoke + 상태 diff |
| S3 | none enter | 선택 생략도 `none` fallback 장면으로 살아남는가 | Commit 1 | 프로그래머 | smoke + 상태 diff |
| S4 | theme confirm resolve | 책 전리품 적용이 confirm 시점에 정확히 1회 일어나는가 | Commit 2 | 프로그래머/QA | smoke + resolve diff |
| S5 | enemy confirm resolve | 적 전리품도 confirm 1회 적용/정리로 닫히는가 | Commit 2 | 프로그래머/QA | smoke + resolve diff |
| S6 | none confirm resolve | 무보상 진행도 끊기지 않고 다음 세그먼트로 넘어가는가 | Commit 2 | 프로그래머/QA | smoke + resolve diff |
| S7 | invalid reward recovery | 잘못된 reward가 `none`으로 강등되어 런이 사는가 | Commit 1 | 프로그래머/QA | smoke + normalize diff |
| S8 | missing snapshot recovery | 저장 snapshot 누락도 재구성 또는 `none`으로 수렴하는가 | Commit 1 | 프로그래머/QA | smoke + normalize diff |
| S9 | broken sourceType recovery | 이상한 sourceType이 기본 branch로 복구되는가 | Commit 1 | 프로그래머/QA | smoke + normalize diff |
| S10 | resolved reload guard | 이미 해결된 상태를 재실행하지 않는가 | Commit 1 + 2 | 프로그래머/QA | smoke + 중복 적용 부재 |
| S11 | missing copy reinjection | 누락 카피가 빈 카드가 아니라 기본 텍스트로 살아나는가 | Commit 1 | 프로그래머/UI | smoke + DOM evidence |
| S12 | visual density sanity | theme/enemy/none 3축이 카드 1장 + CTA 1개 + footnote 1줄 밀도를 유지하는가 | Commit 2 | UI/아트/QA | DOM/screenshot + smoke |

### 1-2. Commit 1 종료선
**Commit 1이 green이려면 아래가 동시에 참이어야 한다.**
- S1/S2/S3 enter 3종이 모두 `phase='overrunEvent'`와 canonical snapshot을 만든다.
- S7/S8/S9 recovery 3종이 모두 `none` 또는 정상 snapshot으로 수렴한다.
- S10이 최소한 `resolved` 상태를 안전하게 읽고 재실행하지 않는 바닥을 갖는다.
- S11이 누락 카피를 기본 텍스트로 재주입한다.
- 아직 `S4/S5/S6/S12`는 green이 아니어야 정상이다.

### 1-3. Commit 2 종료선
**Commit 2가 green이려면 아래가 동시에 참이어야 한다.**
- S4/S5/S6 confirm 3종이 모두 reward 1회 적용 + page 전진 + cleanup을 닫는다.
- S10이 reload/reclick 중복 적용 금지를 실제 closing 증거로 보여 준다.
- S12가 theme/enemy/none 3축에서 같은 장면 위계와 밀도를 유지한다.
- old immediate baseline이 smoke와 코드 모두에서 제거된다.
- Commit 2 green 전까지 B-track 온보딩은 열리지 않는다.

---

## 2. 픽스처별 상태 / 함수 앵커

### 2-1. 엔진 앵커
| 픽스처 | 핵심 상태 | 함수/경계 | 아직 바뀌면 안 되는 것 |
| --- | --- | --- | --- |
| S1~S3 | `phase='overrunEvent'`, `overrunEvent.{contentKey,badgeLabel,accentKey,artTokenSet,headerTitle,flavorText,confirmLabel,resolved}` | `continueOverrun()` 또는 동등 enter wrapper, `buildCanonicalOverrunEventSnapshot()`, `buildNoneOverrunEventSnapshot()` | `player` 자원, `currentPage`, `pagePlan`, `segmentTrophyTemplateIds` |
| S7~S11 | invalid/missing/broken payload가 safe recovery로 수렴 | `normalizeOverrunEventState()` | reward apply, page advance |
| S4~S6 | confirm 시점 reward 1회 적용 + segment advance | `resolveOverrunEvent(state)` 또는 동등 함수 | 중복 적용, double-advance |
| S10 | resolved 상태 재실행 금지 | resolve guard + reload path | reward 2회 적용 |

### 2-2. 렌더 앵커
| 픽스처 | render surface | DOM / 시각 기준 | 금지 패턴 |
| --- | --- | --- | --- |
| S11 | `renderOverrunEvent(summary)` 이전 normalize 결과 | 헤더/플레이버/CTA가 빈 문자열로 남지 않음 | title/sourceType 역추론 |
| S12 | `renderOverrunEvent(summary)` | `badge -> header -> flavor -> reward summary -> CTA -> footnote` 위계 유지 | reward 화면/ending 화면 그대로 재사용 |
| S3 | `none` branch | 카드 1장과 CTA가 사라지지 않음 | placeholder 빈 상태, disabled-only CTA |

### 2-3. 문서 앵커
| 픽스처군 | 우선 문서 | 보조 문서 |
| --- | --- | --- |
| S1~S3 | `dicespell_overrun_first_runtime_slice_packet_kr.md` | `state_matrix`, `smoke_migration_packet` |
| S4~S6 | `dicespell_overrun_second_runtime_slice_packet_kr.md` | `second_runtime_review_packet`, `runtime_handoff_scorecard` |
| S7~S11 | `dicespell_overrun_event_smoke_migration_packet_kr.md` | `a2_snapshot_packet`, `first_runtime_review_packet` |
| S12 | `dicespell_overrun_a3_ui_packet_kr.md` | `ending_overrun_boundary_packet`, `content_deck` |

---

## 3. UI / DOM evidence 레저

### 3-1. UI 오너가 이번 턴에 실제로 제출해야 하는 것
| 픽스처 | 최소 화면 증거 | 왜 필요한가 | 아직 하지 말아야 하는 것 |
| --- | --- | --- | --- |
| S11 | 누락 카피 재주입 후 헤더/CTA가 살아 있는 DOM evidence 1건 | 빈 상태 카드 방지 | 전체 visual polish |
| S12-theme | `themeTrophy` 카드 1장 캡처 | 책 전리품 branch signifier 확인 | 새 카드 시스템 개편 |
| S12-enemy | `enemyTrophy` 카드 1장 캡처 | 적 전리품 branch signifier 확인 | 보상 카드 리디자인 |
| S12-none | `none` 카드 1장 캡처 | fallback도 실제 장면임을 증명 | placeholder 화면 처리 |
| boundary compare | `ending` vs `overrunEvent` 비교 캡처 1건 | 결과 화면과 경계 장면 분리 확인 | ending에 flavor 재주입 |

### 3-2. 아트 오너가 확인해야 하는 것
| 체크 | 기대값 | 실패 예시 |
| --- | --- | --- |
| badge signifier | theme/enemy/none이 최소 색/형태/레이블로 구분됨 | 셋 다 같은 금색 배지 |
| accent drift | `accentKey` 기준 색과 톤이 branch마다 유지됨 | 코드/스타일 임의 덮어쓰기 |
| neutral density | `none`도 빈 화면이 아니라 중립 카드 밀도를 유지 | CTA만 남고 카드 shell 소실 |
| boundary tone | ending과 `overrunEvent` shell/tone이 다름 | 결과표 재사용으로 구분 불가 |

---

## 4. 오너별 handoff 포인트

### 프로그래머에게 넘길 것
- Commit 1에서 green이어야 하는 픽스처는 `S1/S2/S3/S7/S8/S9/S10/S11` 뿐이다. `S4/S5/S6/S12`를 같이 닫으려 하면 범위가 커진다.
- Commit 2에서는 반대로 `S4/S5/S6/S10/S12`만 닫는다. 이때 `S10`은 reload/reclick 중복 적용 방지의 closing 증거로 승격된다.
- smoke 이름, helper 이름, review 코멘트에서 반드시 `S번호`를 그대로 남긴다. `theme enter`, `recovery`, `ui sanity` 같은 느슨한 이름만 쓰면 다시 문서와 코드가 어긋난다.

### UI에게 넘길 것
- Commit 1에서는 `S11` 외에는 shell polish를 마감하지 않는다.
- Commit 2 제출물은 `S12-theme / S12-enemy / S12-none / ending-vs-overrun compare` 4종이면 충분하다.
- `none`은 QA 편의용 케이스가 아니라 제품 fallback이므로 theme/enemy와 같은 우선순위로 캡처를 남긴다.

### 아트에게 넘길 것
- 이번 턴 산출물은 대형 신규 자산이 아니라 `badge/accent/backplate drift` 확인이다.
- `S12-none`이 비어 보이면 실패다. 중립 branch도 시각적으로 `장면`이어야 한다.
- `themeTrophy`와 `enemyTrophy`는 서로 다른 기억점이어야 하고, 그 차이는 최소 배지/톤에서 바로 읽혀야 한다.

### QA에게 넘길 것
- 테스트 보고는 반드시 `S번호`로 남긴다. 예: `S4/S5/S6 green`, `S10 guard fail`.
- Commit 1 green과 Commit 2 green을 같은 코멘트로 묶지 않는다.
- `S7~S11`은 옵션 recovery가 아니라 save/load 생존선이다. 누락되면 closing 이전이라도 fail 처리한다.

### PM/기획에게 넘길 것
- tracker/run log 요약은 `픽스처군` 기준으로 쓴다. 예: `enter/recovery floor`, `scene/resolve closing`.
- Commit 1 green 뒤에는 `A-track이 닫혔다`고 쓰지 않는다. `Commit 2만 열 수 있다`까지만 쓴다.
- Commit 2 green 뒤에만 B-track `B-1 library`를 다음 액션으로 올린다.

---

## 5. 리뷰 / 제출 순서

### 권장 제출 순서
1. **smoke 결과** — 어떤 `S번호`가 green인지 먼저 적는다.
2. **상태 diff 메모** — Commit 1이면 non-change line, Commit 2면 1회 적용 line을 적는다.
3. **UI/DOM evidence** — S11 또는 S12 관련 캡처를 붙인다.
4. **코드 diff 요약** — 어느 함수/파일이 이번 픽스처군을 닫는지 쓴다.
5. **handoff 문장** — tracker/run log용 한 줄을 남긴다.

### Commit 1 복붙 문장
> 이번 커밋은 `S1/S2/S3 + S7~S11` 기준으로 `overrunEvent` enter/recovery floor만 닫았고, `S4/S5/S6/S12`에 해당하는 scene/resolve closing은 아직 열지 않았다.

### Commit 2 복붙 문장
> 이번 커밋은 `S4/S5/S6 + S10 + S12` 기준으로 `overrunEvent` scene/resolve closing을 닫아 reward 1회 적용, `none` 무보상 진행, `resolved` reload guard, visual density sanity, old immediate baseline 제거까지 마무리했다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| S번호 없이 느슨한 말로 기록하는 위험 | 문서와 코드/QA 기록이 다시 엇갈린다 | fixture 번호/커밋/오너/증거를 같은 표로 고정 | 첫 실제 Commit 1 기록 시 |
| Commit 1에서 S12까지 보려는 위험 | floor와 closing이 다시 섞인다 | Commit 1/2 배치표로 허용 픽스처를 분리 | Commit 1 착수 직전 |
| Commit 2에서 S7~S11 recovery를 빼먹는 위험 | 저장 경계가 closing green 뒤에도 불안정하다 | recovery는 Commit 1 floor의 필수 선행조건이라고 명시 | Commit 2 리뷰 직전 |
| UI/아트가 `none` branch를 보조 상태로 취급하는 위험 | fallback 장면이 실제 사용자 경험에서 비어 보인다 | S12-none을 독립 제출물로 고정 | Commit 2 UI review 시 |
| PM이 fixture군 구분 없이 `오버런 컷오버 완료`로 기록하는 위험 | half-cutover가 완성처럼 보인다 | Commit 1/2 복붙 문장을 분리 | tracker/run log 작성 시 |

---

## 7. 즉시 다음 액션
1. implementer는 `smoke migration packet -> first runtime slice -> state matrix -> second runtime slice -> runtime handoff scorecard -> 이 레저` 순서로 `S번호`와 커밋 범위를 먼저 맞춘다.
2. Commit 1에는 `S1/S2/S3/S7/S8/S9/S10/S11`만, Commit 2에는 `S4/S5/S6/S10/S12`만 남긴다.
3. 리뷰 코멘트와 tracker/run log에 `S번호`를 그대로 적는다.
4. Commit 2 green 전에는 onboarding B-track을 열지 않는다.
5. 새 문서를 더 만들기보다, 실제 코드/테스트/캡처 증거가 이 레저 번호와 같은지부터 맞춘다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_event_smoke_migration_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_second_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_second_runtime_review_packet_kr.md`
- `docs/dicespell_overrun_runtime_handoff_scorecard_kr.md`
- `docs/dicespell_overrun_onboarding_owner_handoff_packet_kr.md`
