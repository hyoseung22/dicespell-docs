# DiceSpell `Run Table` Token Follow-up Reopen Packet

## 3줄 요약
- 이 문서는 `queue readiness board`, `queue closing packet`, `queue signoff ladder`까지 이미 충분한 상태에서도 끝까지 남아 있던 마지막 운영 공백인 **`좋아, queue closing 뒤에 후속 수정이 생기면 정확히 어떤 이름으로, 어떤 범위로, 누가 reopen을 열고 어디까지는 그대로 green으로 유지하지?`**를 고정하는 source-of-truth다.
- 핵심은 새 stage를 더 설계하는 것이 아니라, `follow-up reopen`을 **`기존 green 취소`가 아니라 `bounded packet 하나만 다시 여는 운영 행위`**로 다시 정의해 tracker / run log / portal / manifest / git에서 마지막 signoff가 다시 흔들리지 않게 만드는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `무엇을 reopen 사유로 적을 수 있는지`, `무엇은 여전히 못 여는지`, `어떤 문장을 복붙해야 기존 atlas / battle / oath green을 보존하는지`가 바로 보여야 한다.

## 한 줄 목표
`Run Table token queue closing` 뒤 follow-up이 필요해도 **기존 stage green은 유지한 채 새 bounded packet 하나만 열도록** reopen naming / owner / archive / 기록 문장을 file-level로 잠근다.

## 왜 이 문서가 필요한가
지금 Run Table token 축은 마지막 운영선까지 이미 많이 잠겨 있다.

- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_signoff_ladder_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_atlas_handoff_scorecard_kr.md`
- `docs/dicespell_game_flow_token_battle_handoff_scorecard_kr.md`
- `docs/dicespell_game_flow_token_oath_handoff_scorecard_kr.md`

즉 아래 질문은 이미 정리돼 있다.

- stage evidence는 누가 제출하고 누가 candidate / green / queue closing을 기록하는가
- `oath-banner green`, `queue-closing review-ready`, `Run Table token queue closing`은 왜 서로 다른가
- 큰 총괄 green 문장은 왜 금지되는가
- follow-up이 생겨도 기존 green을 지우지 말아야 한다는 점

하지만 실제 마지막 운영 직전과 직후에는 여전히 작은데 중요한 공백이 남는다.

1. `follow-up reopen`을 허용한다고만 써 두면, 실제로는 `무슨 이름의 packet만 열 수 있는지`, `어느 stage부터 다시 candidate로 되돌리는지`, `무엇은 그대로 green인지`가 사람마다 달라진다.
2. reopen 이유가 `ending helper도 조금`, `reward/shop side family도 같이`, `queue wording도 같이` 같은 큰 메모로 흘러가면, bounded reopen이 다시 total-green 취소처럼 보인다.
3. archive path와 handoff line이 없으면 follow-up이 `새 작업`인지 `기존 closing 철회`인지가 run log / portal / git에서 다시 흐려진다.
4. QA/PM이 reopen을 여는 시점에 `review-ready`와 `reopen opened`를 같은 줄에 쓰면, 기존 atlas / battle / oath green이 아직 유효한지 아닌지가 다시 애매해진다.

즉 지금 필요한 것은 새 queue packet이 아니라, **reopen을 기존 green 보존형 bounded packet으로 다시 잠그는 follow-up reopen packet**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 이 문서에서 얻어야 하는 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `2. reopen boundary`, `4. role-specific handoff` | `queue signoff ladder`, `source-of-truth map` | reopen은 새 큰 polish turn이 아니라 packet 하나만 다시 여는 bounded edit다 |
| UI | `2. reopen boundary`, `3. reopen packet naming`, `5. archive / 기록 규칙` | `queue closing packet`, `oath handoff scorecard` | 기존 green을 지우지 않고 새 visual follow-up만 별도 packet으로 남긴다 |
| 아트 | `3. reopen packet naming`, `4. role-specific handoff` | `evidence manifest`, `queue signoff ladder` | art follow-up은 새 starter sanity packet명과 범위를 먼저 적은 뒤에만 열린다 |
| QA | `1. reopen ladder`, `5. archive / 기록 규칙`, `6. 금지 문장` | `queue signoff ladder`, `queue closing packet` | QA는 reopen 필요를 메모할 수 있지만 기존 green 취소 문장은 쓰지 않는다 |
| PM/기획 | `0. first screen 결론`, `1. reopen ladder`, `3. reopen packet naming` | `queue signoff ladder`, `source-of-truth map`, `gap ledger` | PM은 reopen 이름 / 범위 / 비범위 / 유지되는 green을 같이 적는 마지막 기록자다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- queue closing 뒤 follow-up이 생기면 정확히 무엇을 reopen이라고 부르지?
- reopen은 언제 기존 green 유지이고 언제 새 candidate / green을 다시 요구하지?
- tracker / run log / portal / git에는 어떤 문장을 그대로 써야 하지?

### 가장 짧은 답
- `follow-up reopen`은 **기존 atlas / battle / oath green을 지우지 않고** 새 bounded packet 하나만 여는 운영 문장이다.
- reopen은 `문제 설명`, `새 packet 이름`, `이번에 여는 범위`, `이번에도 안 여는 범위`, `유지되는 green`을 같이 적어야 한다.
- reopen 이름 없이 `조금 더 다듬기`, `마지막 polish`, `전체 후속`처럼 쓰면 안 된다.
- reopen이 열려도 queue closing history는 유지되며, 새 work item만 별도 candidate / green 사다리로 다시 관리한다.

### 한 줄 판정 규칙
좋은 reopen은 **`무엇이 다시 열린다`보다 `무엇이 그대로 green으로 남는다`를 먼저 말하는 reopen**이다.

---

## 1. reopen ladder

## 3줄 요약
- reopen도 `문제 발견 -> reopen 제안 -> reopen opened -> 새 packet candidate/green` 순서로 읽어야 한다.
- 핵심은 기존 queue closing을 취소하지 않는 것이다.
- reopen은 마지막 운영선의 rollback이 아니라, 마지막 운영선 위에 얹는 bounded addendum이다.

### 한 줄 목표
follow-up을 `closing 취소`가 아니라 **`새 packet만 열린 추가 작업`**으로 못 박는다.

| 단계 | 누가 먼저 적는가 | 필요한 입력 | 아직 금지 |
| --- | --- | --- | --- |
| `follow-up needed` 메모 | 프로그래머 / UI / 아트 / QA | 발견된 drift 1개, 영향 stage 1개, 크게 안 여는 범위 | reopen opened, green 취소 |
| `follow-up reopen proposal` | QA 또는 PM/기획 | bounded packet 이름 초안, 왜 reopen인지, 유지되는 green 명시 | 새 total-green 부정, 기존 queue closing 삭제 |
| `Run Table token follow-up reopen` | PM/기획 | packet 이름 확정, owner, archive path, non-change line | `complete 취소`, `전면 재오픈` |
| 새 packet `candidate / green` | 해당 stage owner + QA + PM | 새 packet evidence bundle | 기존 atlas/battle/oath green 무효화 문장 |

### reopen이 가능한 조건
1. 기존 `queue closing` 기록과 stage green 3개가 남아 있다.
2. 후속 이슈가 새 bounded packet 이름으로 설명 가능하다.
3. 영향 범위가 atlas / battle / oath 중 하나 또는 명시적 cross-stage packet 하나로 좁혀진다.
4. reopen 메모가 `무엇은 안 연다`를 같이 적는다.
5. 기존 archive path와 새 archive path를 분리해 기록할 수 있다.

---

## 2. reopen boundary

## 3줄 요약
- reopen은 `조금만 더`가 아니라 범위가 붙은 새 packet이다.
- 특히 마지막 운영선에서는 cross-stage reopen이 가장 쉽게 무분별하게 커진다.
- 그래서 reopen은 `allowed scope`와 `still locked`를 같이 적어야 한다.

### 한 줄 목표
reopen을 **`열리는 범위`와 `계속 잠겨 있는 범위`**로 동시에 정의한다.

| reopen 유형 | 허용 범위 | 여전히 잠겨 있는 범위 |
| --- | --- | --- |
| atlas follow-up | route node hook / threshold / atlas-only archive wording | battle stakes, oath banner, queue closing wording |
| battle follow-up | current/next/grimoire stakes bundle, battle-only density / handoff | atlas route node, oath banner, ending helper |
| oath follow-up | banner/result/carry role, continue-overrun CTA wording, oath-only archive wording | ending helper 전체 개편, reward/shop side family, queue closing wording 재정의 |
| queue wording follow-up | queue signoff / queue closing / reopen wording 자체 | stage markup/CSS, capture 재생산, surface redesign |
| cross-stage bounded follow-up | 명시적으로 이름 붙인 연결 packet 1개 | `Run Table 전체`, `Overrun Oath 전체`, `전 UI` 같은 큰 범위 |

### reopen에서 반드시 적을 것
- `이번에 여는 packet 이름`
- `이번 packet owner`
- `영향 stage`
- `이번에 열지 않는 범위`
- `유지되는 기존 green`
- `새 archive 경로`

---

## 3. reopen packet naming

## 3줄 요약
- reopen은 이름이 없으면 거의 항상 큰 메모로 흐른다.
- 좋은 reopen 이름은 stage / 목적 / 범위를 한 줄에서 말해야 한다.
- 나쁜 reopen 이름은 `follow-up`, `cleanup`, `final polish`, `misc fix`처럼 아무 stage도 말하지 않는다.

### 한 줄 목표
reopen을 **packet 이름**으로만 연다.

### 권장 패턴
- `run-table-atlas-threshold-focus-followup`
- `run-table-battle-stakes-density-followup`
- `run-table-oath-banner-cta-copy-followup`
- `run-table-queue-wording-followup`
- `run-table-oath-boundary-note-followup`

### 금지 패턴
- `run-table-final-polish`
- `run-table-followup`
- `ending-cleanup`
- `ui-final-pass`
- `misc-refine`

### packet 이름 체크리스트
1. stage가 보이는가
2. 무엇을 다시 여는지가 보이는가
3. queue closing 취소처럼 읽히지 않는가
4. 새 archive 폴더명으로 바로 쓸 수 있는가
5. handoff line에 복붙해도 과장되지 않는가

---

## 4. role-specific handoff points

### 프로그래머에게 넘길 것
- reopen 제안은 버그/드리프트를 stage 1개 또는 bounded packet 1개로만 적는다.
- `이 김에` 다른 surface를 같이 여는 제안은 금지다.
- 기존 stage green과 queue closing은 그대로 남겨 둔다.

### UI에게 넘길 것
- visual follow-up은 capture 1~2장과 density/boundary note로 먼저 좁힌다.
- `전반적인 톤 재정리`는 reopen 이름이 아니다.
- summary 문장이 아니라 packet 이름과 non-change line을 먼저 적는다.

### 아트에게 넘길 것
- asset follow-up도 `starter sanity drift 1건`처럼 좁힌다.
- 기존 starter pass를 취소하는 문장을 쓰지 않는다.
- 새 asset archive는 reopen packet용 별도 경로에 둔다.

### QA에게 넘길 것
- QA는 reopen 필요를 메모할 수 있지만 기존 green 취소 문장은 쓰지 않는다.
- `queue-closing review-ready`와 `follow-up reopen proposal`은 같은 줄에 쓰지 않는다.
- reopen opened 뒤에는 새 packet candidate/green 사다리로만 다시 본다.

### PM/기획에게 넘길 것
- PM은 reopen 이름 / 범위 / 비범위 / 유지 green / 새 archive 경로를 함께 적는 마지막 기록자다.
- `Run Table token follow-up reopen`은 reopen 열기 문장이지 기존 closing 철회 문장이 아니다.
- PM도 새 packet 이름 없이는 reopen을 열지 않는다.

---

## 5. archive / 기록 규칙

## 3줄 요약
- reopen이 실패하는 가장 흔한 이유는 wording보다 archive 충돌이다.
- 기존 archive와 새 follow-up archive를 분리하지 않으면 rollback처럼 보인다.
- 그래서 reopen도 폴더 / manifest / handoff line을 독립적으로 가져야 한다.

### 한 줄 목표
reopen을 **새 archive bundle 1개**로 남긴다.

### archive 규칙
- 기존 stage archive는 그대로 둔다.
- reopen은 `docs/artifacts/run-table-token-delivery/followups/<packet-name>/` 같은 새 경로를 만든다.
- 새 경로에는 최소 `manifest.md`, `notes/`, 필요 시 `captures/`를 둔다.
- manifest 첫 줄에는 유지되는 기존 green을 같이 적는다.

### 복붙용 reopen 문장
- `Run Table token follow-up reopen: <packet-name> only, previous atlas-route-node / battle-stakes-plaque / oath-banner green 유지.`
- `Follow-up scope: <what-opens>. Still locked: <what-stays-locked>.`
- `Archive: docs/artifacts/run-table-token-delivery/followups/<packet-name>/`

### 기록 위치별 규칙
| 위치 | 허용 문장 | 금지 |
| --- | --- | --- |
| tracker | `follow-up reopen` + packet 이름 + 유지 green | `queue closing 취소`, `다시 미완료` |
| automation run log | timestamped reopen note + 새 archive path | 이전 closing entry 재작성 |
| portal/index | 새 follow-up summary 링크 추가 | 기존 green 링크 삭제 |
| manifest | 유지 green + 이번 reopen 범위 + non-change | 큰 총괄 reopening |
| git commit | `docs: add run table oath banner cta follow-up packet` 수준의 사실 문장 | `reopen run table`, `undo closing` |

---

## 6. 금지 문장 / 금지 행동

## 3줄 요약
- reopen의 가장 큰 적은 모호한 말과 rollback처럼 들리는 말이다.
- 마지막 운영선에서는 작은 wording drift가 history 전체를 흔든다.
- 아래 표현은 모두 기존 green을 불필요하게 흐린다.

### 한 줄 목표
reopen을 **작게 열고 명확하게 적는다.**

### 금지 문장
- `Run Table closing 취소`
- `다시 미완료`
- `전면 재오픈`
- `이번엔 전체 polish`
- `사실상 다시 작업`
- `oath 다시 처음부터`

### 금지 행동
- 기존 atlas / battle / oath green 지우기
- queue closing entry 본문 대규모 수정하기
- packet 이름 없이 reopen 열기
- 새 archive 없이 기존 stage archive 안에 follow-up 섞기
- `review-ready`와 `reopen opened`를 같은 줄에 적기

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| reopen이 기존 queue closing 취소처럼 읽히는 위험 | 마지막 운영 history 전체가 rollback처럼 보이고 stage green 의미가 약해진다 | reopen을 새 packet 이름 + 유지 green + non-change line으로만 열도록 고정했다 | 첫 reopen 제안 직전 |
| packet 이름 없이 `follow-up`, `final polish` 같은 큰 메모가 쓰이는 위험 | bounded reopen이 다시 total polish로 커진다 | 권장/금지 packet naming 패턴을 분리 고정했다 | tracker / run log 작성 직전 |
| 기존 archive와 follow-up archive가 섞이는 위험 | reviewer가 무엇이 original green이고 무엇이 reopen evidence인지 다시 수집해야 한다 | follow-up 전용 새 archive 경로 규칙을 추가했다 | 첫 follow-up archive 생성 직전 |
| QA/PM이 reopen proposal과 reopen opened를 같은 줄에 적는 위험 | ownership과 signoff 층위가 다시 흐려진다 | reopen ladder를 `proposal` / `opened` / `새 packet candidate/green`으로 분리했다 | reopen review-ready 직전 |

---

## 8. immediate next actions

1. 다음 queue follow-up 판단은 `queue readiness board -> queue closing packet -> queue signoff ladder -> 이 follow-up reopen packet` 순서로 읽는다.
2. follow-up이 필요하면 먼저 `무엇이 그대로 green인지`와 `무엇을 아직 열지 않는지`를 적는다.
3. packet 이름이 없으면 reopen을 열지 않는다.
4. 기존 queue closing / atlas / battle / oath green entry는 수정하지 않고, 새 timestamped reopen note만 append한다.
5. 새 작업은 반드시 새 follow-up archive 경로와 새 packet candidate/green 사다리로 닫는다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_queue_readiness_board_kr.md`
- `docs/dicespell_game_flow_token_queue_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_queue_signoff_ladder_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 마지막 작은데도 실제로는 가장 쉽게 흔들리는 공백은 **`좋아, queue closing 뒤에 follow-up이 생기면 그건 기존 green 취소야, 아니면 새 packet 하나만 여는 거야?`**였다. 이 문서는 그 공백을 reopen ladder, packet naming, archive/기록 규칙, 금지 문장까지 한 장으로 다시 잠가, 마지막 운영선에서도 follow-up이 bounded packet으로만 열리고 기존 atlas / battle / oath green은 끝까지 유지되게 만드는 reopen source-of-truth다.
