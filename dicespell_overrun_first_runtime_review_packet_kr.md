# DiceSpell `overrunEvent` 첫 런타임 슬라이스 리뷰 패킷

## 3줄 요약
- 이 문서는 `first runtime slice`가 실제로 닫혔다고 말하기 위한 **리뷰 기준선**을 한 장으로 고정하는 source handoff다.
- 구현 범위는 이미 `first runtime slice packet`과 `state matrix`로 좁혀졌지만, 리뷰 단계에서 무엇을 봐야 하는지가 흐리면 다시 half-cutover가 남는다.
- 목표는 프로그래머/QA/UI/아트/PM이 같은 evidence bundle을 보고 `이 커밋은 A-1/A-2까지만 닫혔다`를 같은 문장으로 말하게 만드는 것이다.

## 한 줄 목표
`A-1 enter 분리 + A-2 canonical snapshot floor` 커밋을 **리뷰 가능한 단위**로 다시 고정해, `무엇이 바뀌었는가`와 `무엇이 아직 안 바뀌었는가`를 같은 증거 묶음으로 확인하게 만든다.

## 왜 이 문서가 필요한가
지금 DiceSpell에는 이미 아래 문서가 있다.

- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
- `docs/dicespell_overrun_runtime_patch_map_kr.md`
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_a1_engine_cutover_packet_kr.md`
- `docs/dicespell_overrun_a2_snapshot_packet_kr.md`

문제는 실구현 직전에도 아래 혼선이 남는다는 점이다.

1. 구현 범위는 좁혀졌지만, 리뷰어가 **어떤 diff를 기대해야 하는지**를 다시 머릿속으로 조합해야 한다.
2. smoke가 통과해도 `reward 미적용 유지`, `currentPage/pagePlan/segmentTrophyTemplateIds 미변경` 같은 비변경선이 리뷰 체크리스트에 없으면 half-cutover가 통과할 수 있다.
3. UI/아트는 `payload freeze는 됐는가`, `화면 polish는 아직 안 열렸는가`를 코드 diff만 보고 판단하기 어렵다.
4. PM/기획은 `좋아, 이 커밋은 어디까지를 끝낸 것인가`를 문서 여러 장 없이 짧게 파악할 요약형 리뷰 문장이 필요하다.

즉 지금 필요한 것은 새 설계가 아니라, **첫 runtime slice 커밋의 리뷰 패키지와 pass/fail 기준**을 한 장으로 압축하는 일이다.

---

## 누가 어디부터 읽어야 하나

| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 |
| --- | --- | --- |
| 프로그래머 | `1. 리뷰 범위`, `2. diff 기대값`, `3. evidence bundle` | `dicespell_overrun_first_runtime_slice_packet_kr.md` |
| QA | `3. evidence bundle`, `4. 비변경선 체크`, `6. fail 조건` | `dicespell_overrun_first_runtime_state_matrix_kr.md` |
| UI | `4. 비변경선 체크`, `5. 역할별 handoff` | `dicespell_overrun_a3_ui_packet_kr.md` |
| 아트 | `4. 비변경선 체크`, `5. 역할별 handoff` | `dicespell_overrun_event_screen_packet_kr.md` |
| PM/기획 | `0. 리뷰 한 줄 결론`, `7. 즉시 다음 액션` | `dicespell_overrun_cutover_runway_packet_kr.md` |

---

## 0. 리뷰 한 줄 결론 템플릿

### 한 줄 목표
리뷰 완료 후 누구나 같은 문장으로 현재 커밋 상태를 말할 수 있게 한다.

아래 문장을 그대로 써도 된다.

> 이번 커밋은 `ending -> overrunEvent` **진입과 canonical snapshot 저장/복구까지만** 닫혔고, reward apply / `currentPage` 증가 / `pagePlan` 확장 / `segmentTrophyTemplateIds` 초기화 / 중앙 카드 UI polish / confirm resolve는 아직 열지 않았다.

### 이 문장을 써야 하는 이유
- `무엇이 됐는가`와 `무엇이 아직 안 됐는가`가 같이 들어간다.
- A-3/A-4를 실수로 포함한 것처럼 과장하지 않는다.
- 다음 슬롯이 어디서 이어져야 하는지 바로 보인다.

---

## 1. 이번 리뷰 범위

## 3줄 요약
- 리뷰 범위는 `first runtime slice packet`과 동일하게 **A-1 enter + A-2 payload floor**까지만 본다.
- UI 완성도보다 state/normalize/recovery가 먼저 pass해야 한다.
- 이 범위를 넘는 diff가 들어오면 좋은 코드여도 이번 커밋 기준으로는 fail이다.

## 한 줄 목표
리뷰어가 `좋아 보이니까 통과`가 아니라 **지금 커밋에 허용된 범위**를 먼저 확인하게 만든다.

| 범주 | 이번 리뷰에서 pass 대상 | 이번 리뷰에서 fail 대상 |
| --- | --- | --- |
| 엔진 | `continueOverrun()` enter wrapper화, canonical snapshot builder, normalize helper | reward apply, progression finalize, `resolved=true` 선적용 |
| 앱 | `phase='overrunEvent'` 최소 branch, summary/snapshot 전달선 유지 | 중앙 카드 1장 위계 완성, 최종 CTA density |
| 스타일 | 없음 또는 최소 placeholder | shell/tone/polish 전개 |
| 검증 | enter smoke, canonical field set smoke, legacy/invalid/missing recovery smoke | full resolve smoke, visual sanity를 억지로 같은 커밋에 넣기 |

---

## 2. diff 기대값

## 한 줄 목표
리뷰어가 PR/커밋 diff에서 **어떤 파일과 함수가 바뀌는 것이 정상인지** 먼저 잡게 만든다.

| 파일 | 기대 diff | 리뷰 포인트 |
| --- | --- | --- |
| `dice-spell-standalone/src/game-engine.js` | `continueOverrun()`가 즉시 적용 체인에서 enter wrapper로 줄어든다. canonical snapshot helper와 normalize helper가 추가된다. | `phase`와 snapshot만 바꾸고 progression은 건드리지 않았는가 |
| `dice-spell-standalone/src/app.js` | `phase='overrunEvent'` 최소 explicit branch가 생긴다. | display-only placeholder 수준인지, A-3 UI 구현이 섞이지 않았는가 |
| `dice-spell-standalone/scripts/smoke-test.mjs` | enter smoke + canonical field set smoke + recovery smoke가 추가된다. | happy path만이 아니라 legacy/missing/invalid가 들어갔는가 |

### 정상적인 작은 diff 패턴
- `continueOverrun()` 내부에서 `applyEffectBundle` 호출이 빠지거나 뒤로 밀린다.
- `buildCanonicalOverrunEventSnapshot()` 또는 동등 helper가 생긴다.
- `normalizeOverrunEventState()` 또는 동등 helper가 생긴다.
- `renderCenter(summary)`에 `overrunEvent` 분기가 생기되, layout/class 폭발은 아직 없다.
- smoke fixture가 `selectedRewardId만 있는 legacy`, `invalid rewardId`, `missing snapshot`, `skip/none`을 각각 다룬다.

### 경계 신호
아래가 보이면 리뷰를 멈추고 범위 초과 여부를 다시 본다.
- `styles.css` 대규모 수정
- `confirm-overrun-event` 실제 resolve wiring
- `currentPage += 1` / `pagePlan.push(...)` / `segmentTrophyTemplateIds = []`
- `resolved = true`를 enter 시점에 넣는 처리
- `reward.title`/`sourceType`로 badge/accent/header를 다시 계산하는 로직

---

## 3. evidence bundle

## 3줄 요약
- 이 슬라이스는 코드보다 **증거 묶음**으로 pass/fail을 판단해야 한다.
- 증거는 `코드 diff`, `state diff`, `smoke 결과`, `짧은 human summary` 네 가지가 같이 있어야 한다.
- 하나라도 빠지면 구현은 됐어도 handoff-ready라고 보기 어렵다.

## 한 줄 목표
리뷰가 감상평이 아니라 **같은 증거 묶음 확인**으로 끝나게 만든다.

### 필수 evidence 4종

| 증거 | 무엇이 보여야 하나 | 왜 필요한가 |
| --- | --- | --- |
| 코드 diff | `continueOverrun()` enter화 + canonical helper + normalize helper + minimal branch | 범위 초과/누락 확인 |
| 상태 diff 메모 | `phase='overrunEvent'`, canonical payload 존재, progression 미실행 | state matrix와 실제 구현 일치 확인 |
| smoke 결과 | enter / canonical / legacy / missing / invalid / none | recovery 포함 여부 확인 |
| 한 줄 summary | `이번 커밋은 enter+snapshot까지만 닫았다` | PM/기획/후속 implementer handoff용 |

### 권장 리뷰 코멘트 템플릿

```md
- 확인 범위: A-1 enter 분리 + A-2 canonical snapshot/normalize
- 확인 결과: `phase='overrunEvent'` 진입, canonical field set 저장, legacy/missing/invalid recovery smoke 확인
- 아직 안 연 범위: reward apply / currentPage 증가 / pagePlan 확장 / segmentTrophyTemplateIds 초기화 / A-3 UI polish / A-4 resolve
```

---

## 4. 비변경선 체크

## 한 줄 목표
리뷰어가 `바뀐 것`만 보지 말고 **아직 안 바뀌어야 하는 것**도 확인하게 만든다.

| 항목 | 이번 리뷰에서 기대값 | 바뀌어 있으면 왜 fail인가 |
| --- | --- | --- |
| reward effect 결과 | 아직 미적용 | enter와 resolve가 다시 섞인 것 |
| `currentPage` | 엔딩 직전 값 유지 | progression이 먼저 열림 |
| `pagePlan.length` | 유지 | 새 세그먼트가 먼저 확정됨 |
| `segmentTrophyTemplateIds` | 유지 | 세그먼트 종료 정리가 너무 일찍 들어감 |
| `resolved` | `false` 또는 기본값 | A-4가 섞였거나 reload guard가 조기 적용됨 |
| UI shell density | placeholder 수준 | A-3 시각 계약이 먼저 들어옴 |
| CTA behavior | display-only 의미 수준 | confirm resolve가 먼저 연결됨 |

### 리뷰어 메모
- 첫 슬라이스는 `보여도 됨`보다 `아직 적용되지 않아야 함`이 더 중요하다.
- 특히 progression 관련 값은 작은 diff라도 들어오면 이번 커밋 기준으로는 fail이다.

---

## 5. 역할별 handoff 포인트

### 프로그래머에게 넘기는 것
- 이 커밋은 enter + snapshot + recovery까지만 닫으면 된다.
- 리뷰어가 `왜 resolve가 없지?`라고 묻는 것이 아니라, `resolve가 아직 없어서 맞다`를 확인해야 한다.
- `state matrix` 문장과 실제 diff가 다르면 코드보다 문장부터 맞춘다.

### UI 오너에게 넘기는 것
- 이번 커밋이 pass이면 다음부터는 `contentKey`/`badgeLabel`/`accentKey`/`artTokenSet`을 믿고 A-3 shell 검토를 시작할 수 있다.
- 아직 요구하지 않는 것: 중앙 카드 위계, footnote density, CTA polish.
- 지금 확인할 것: render가 branch를 재판정하지 않게 되었다는 사실.

### 아트 오너에게 넘기는 것
- 이번 커밋은 asset 제작 턴이 아니라 token freeze 확인 턴이다.
- `artTokenSet`이 canonical payload에 안정적으로 들어오면 다음 슬라이스에서 최소 자산 패키지 범위를 산정한다.
- neutral `none`도 정상 branch로 남아 있는지만 본다.

### QA에게 넘기는 것
- 이번 리뷰는 visual sanity보다 recovery sanity가 우선이다.
- `none`, legacy, missing, invalid 네 축이 다 없으면 pass로 보지 않는다.
- smoke가 green이어도 progression 값이 움직였으면 fail이다.

### PM/기획에게 넘기는 것
- 이 커밋은 `다음 장면의 존재를 안전하게 저장하는 턴`이다.
- 아직 `장면을 예쁘게 보여 주는 턴`도, `버튼을 눌러 실제로 넘어가는 턴`도 아니다.
- 다음 슬롯은 A-3 UI 또는 A-4 resolve 중 하나를 bounded commit으로 여는 방식으로 이어진다.

---

## 6. fail 조건

## 한 줄 목표
리뷰 중 `겉으로는 통과 같아 보이는 실패`를 빠르게 걸러낸다.

1. `phase='overrunEvent'`가 됐지만 canonical field set이 partial이다.
2. `contentKey` 대신 title/sourceType 기반 재추론이 남아 있다.
3. reward effect가 이미 적용됐다.
4. `currentPage`, `pagePlan`, `segmentTrophyTemplateIds` 중 하나라도 움직였다.
5. legacy/missing/invalid가 render fallback에만 의존한다.
6. `none` branch가 비어 있거나 CTA 의미가 없다.
7. `styles.css`/A-3 shell/A-4 resolve가 한 커밋에 섞여 범위가 부풀었다.

---

## 7. 즉시 다음 액션

## 3줄 요약
- 이 리뷰 패킷은 새 설계를 여는 문서가 아니라, 첫 커밋이 닫혔는지 판단하는 문서다.
- pass가 되면 다음 선택지는 둘 중 하나뿐이다: `A-3 UI` 또는 `A-4 resolve`.
- fail이면 다음 액션도 새 설계 추가가 아니라 first runtime slice 경계 복구다.

## 한 줄 목표
리뷰 결과가 바로 다음 bounded action으로 이어지게 만든다.

### pass일 때
1. `state matrix` 기준 비변경선이 지켜졌는지 tracker/run log에 같은 문장으로 기록
2. `A-3 UI packet` 기준 중앙 카드 1장 shell만 여는 커밋을 따로 연다
3. 또는 UI보다 resolve가 더 시급하면 `A-4 resolve packet` 기준으로 한 번 더 범위를 선언하고 연다

### fail일 때
1. 어떤 progression 값이 먼저 움직였는지 확인
2. canonical field set 누락인지, recovery smoke 누락인지 확인
3. A-3/A-4 diff가 섞였으면 다시 first runtime slice 범위로 되돌린다

---

## 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 후속 확인 |
| --- | --- | --- | --- |
| smoke만 통과하고 비변경선 리뷰가 빠지는 위험 | half-cutover 통과 | state diff와 비변경선 체크를 별도 표로 고정 | 첫 구현 PR 리뷰 시 |
| UI/아트가 payload freeze 전 시각 검토를 시작하는 위험 | shell drift | 역할별 handoff에서 착수 조건 명시 | A-3 착수 직전 |
| PM/기획이 커밋 범위를 과대 해석하는 위험 | 일정/기대 어긋남 | 리뷰 한 줄 결론 템플릿 고정 | tracker/run log 기록 시 |
| fail 시 새 문서를 더 쓰려는 위험 | 실행 지연 | fail 대응도 `slice 경계 복구`로 제한 | next writer slot |

---

## 바로 이어서 볼 문서
- `docs/dicespell_overrun_first_runtime_slice_packet_kr.md`
- `docs/dicespell_overrun_first_runtime_state_matrix_kr.md`
- `docs/dicespell_overrun_a3_ui_packet_kr.md`
- `docs/dicespell_overrun_a4_resolve_packet_kr.md`
- `docs/dicespell_overrun_cutover_runway_packet_kr.md`
