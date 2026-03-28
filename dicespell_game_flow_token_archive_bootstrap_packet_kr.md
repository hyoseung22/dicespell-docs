# DiceSpell `Run Table` Token 아카이브 부트스트랩 패킷

## 3줄 요약
- 이 문서는 `token evidence manifest packet`이 정의한 stage archive 구조를 **실제 refinement 턴 직전에 바로 생성 가능한 starter bundle**로 내리는 source-of-truth다.
- 핵심은 새 토큰을 더 설계하는 것이 아니라, `atlas-route-node / battle-stakes-plaque / oath-banner` 3단계를 **어떤 폴더와 어떤 manifest 초안으로 먼저 열어야 evidence discipline이 무너지지 않는지**를 고정하는 것이다.
- 첫 화면만 읽어도 프로그래머 / UI / 아트 / QA / PM이 `오늘 첫 폴더를 어떻게 열지`, `무엇이 아직 비어 있어도 되는지`, `무엇이 비어 있으면 closing 문장을 쓰면 안 되는지`를 바로 판정할 수 있어야 한다.

## 한 줄 목표
`docs/artifacts/run-table-token-delivery/<stage>/`를 **착수 3분 안에 만들 수 있는 bootstrap bundle**로 표준화해, Run Table token refinement가 감상형 캡처 모음이 아니라 실제 stage archive부터 시작하게 만든다.

## 왜 이 문서가 필요한가
지금 Run Table 축은 다음 문서까지 이미 닫혀 있다.

- `docs/dicespell_game_flow_surface_handoff_packet_kr.md`
- `docs/dicespell_game_flow_token_anchor_packet_kr.md`
- `docs/dicespell_game_flow_token_delivery_workboard_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`

즉 `무엇을 닫아야 하는가`, `무슨 evidence가 필요한가`, `무엇이 아직 비어 있는가`는 이미 정리돼 있다.

하지만 실제 refinement 직전 마지막 작은 공백은 여전히 남아 있다.

1. `좋아, atlas-route-node archive를 만든다`는 말은 있는데, 실제 첫 3분 안에 **어떤 디렉터리와 어떤 starter 파일을 먼저 만들어야 하는지**는 다시 생각해야 한다.
2. `evidence manifest`는 필수 파일 이름을 고정했지만, 실제 첫 턴에는 **어떤 manifest 초안과 어떤 placeholder부터 써야 drift가 안 나는지**가 없다.
3. `overview/focus capture`, `hook manifest`, `density review`, `starter asset list`가 정의돼 있어도, bootstrap이 없으면 첫 evidence가 다시 Desktop/Downloads/메신저 첨부로 흩어진다.
4. archive root는 있어도 starter bundle이 없으면 PM/QA가 `아직 bootstrap도 안 됨`과 `거의 closing-ready`를 혼동할 수 있다.

즉 지금 필요한 것은 새 설계가 아니라, **archive 규칙을 실제 첫 생성 동작까지 내린 부트스트랩 패킷**이다.

---

## 이 문서를 누가 어떻게 읽어야 하나

| 역할 | 먼저 읽을 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `0. first screen 결론`, `1. bootstrap 순서`, `3. stage별 starter bundle` | `token evidence manifest`, `token delivery workboard` | 코드보다 먼저 stage 폴더/manifest/check note부터 만든다 |
| UI | `3. stage별 starter bundle`, `4. owner drop rule` | `token closing packet`, `prototype board` | overview/focus 캡처는 bootstrap 때부터 stage별로 분리한다 |
| 아트 | `3. stage별 starter bundle`, `4. owner drop rule`, `6. 리스크` | `token anchor packet`, `token evidence manifest` | 이번 턴 아트는 starter asset sanity note부터 닫는다 |
| QA | `1. bootstrap 순서`, `4. owner drop rule`, `5. green 전 체크` | `token closing packet`, `token gap ledger` | bootstrap-ready와 closing-ready를 같은 말로 쓰면 안 된다 |
| PM/기획 | `0. first screen 결론`, `5. green 전 체크`, `7. immediate next actions` | `token source-of-truth map`, `token gap ledger` | 큰 green 문장 전에 archive-ready 판정부터 해야 한다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `오늘 atlas / battle / oath 중 하나를 실제로 닫기 시작한다면 첫 3분 안에 무엇을 먼저 만들어야 하지?`
- `evidence manifest가 있다는 말과 starter bundle이 있다는 말은 어떻게 다르지?`
- `아직 비어 있어도 되는 것`과 `비어 있으면 handoff line을 쓰면 안 되는 것`은 무엇이지?

### 가장 짧은 답
- 먼저 `docs/artifacts/run-table-token-delivery/<stage>/`를 만든다.
- 그 안에 `captures/`, `notes/`, `checks/`, `assets/`, `manifest.md`를 만든다.
- starter 파일명 또는 첫 artifact를 적고 나서 live markup/CSS edit에 들어간다.
- `handoff-line.md`는 archive-ready가 되기 전에는 초안으로만 둔다.

### 한 줄 판정 규칙
archive 규칙을 읽는 것과 archive starter bundle을 만든 것은 다르다. **착수는 bundle 생성 이후**부터다.

---

## 1. bootstrap 순서

## 3줄 요약
- 아래 순서는 `atlas-route-node`, `battle-stakes-plaque`, `oath-banner` 전부에 공통으로 적용된다.
- 목적은 live markup/CSS edit 전에 evidence 경로를 먼저 고정해, refinement 도중 새 캡처와 메모가 임시 위치로 새지 않게 만드는 것이다.
- `폴더 생성 -> manifest 초안 -> owner별 starter file 생성 -> 첫 evidence 생성` 순서를 벗어나지 않는다.

### 한 줄 목표
Run Table token archive를 결과 정리 장소가 아니라 **착수 직전 작업 바닥**으로 만든다.

| 순서 | 해야 할 일 | 완료 기준 | 금지 |
| --- | --- | --- | --- |
| 1 | stage 폴더 생성 | `docs/artifacts/run-table-token-delivery/<stage>/` 존재 | `atlas`, `battle`, `oath`, `tmp`처럼 임의 축약 이름 사용 |
| 2 | 기본 하위 폴더 생성 | `captures/`, `notes/`, `checks/`, `assets/` 존재 | `misc/`, `images/`, `etc/` 같은 임의 폴더 사용 |
| 3 | manifest 초안 생성 | `manifest.md`에 stage/date/required artifacts/next step/handoff line 초안 존재 | 첫 캡처가 나올 때까지 manifest 미생성 |
| 4 | starter file 생성 또는 예약 | 각 owner가 넣을 첫 artifact 이름이 존재 | `나중에 정리`로 비워 두기 |
| 5 | 첫 evidence 생성 후만 refinement 지속 | capture 또는 hook/check note 1개 이상 존재 | 코드 수정 뒤에 archive 정리 |

### bootstrap 완료 최소선
아래 6개가 모두 있으면 `bootstrap-ready`로 본다.
1. stage 폴더
2. `captures/`
3. `notes/`
4. `checks/`
5. `assets/`
6. `manifest.md` + starter 파일 최소 1개

---

## 2. core structure: stage는 3개만 허용한다

| stage | 뜻 | 왜 따로 분리하는가 |
| --- | --- | --- |
| `atlas-route-node` | Run Atlas route node closing evidence | atlas는 preview/log와 가장 쉽게 섞이므로 board evidence를 따로 모아야 한다 |
| `battle-stakes-plaque` | Battle Table stakes plaque closing evidence | stakes는 battle header extension이지 전체 전투 HUD 증거가 아니다 |
| `oath-banner` | Overrun Oath banner/result/carry closing evidence | ending은 reward/shop family와 가장 쉽게 섞이므로 role evidence를 분리해야 한다 |

### 왜 더 잘게 쪼개지 않는가
- 이번 refinement의 목적은 `surface 3면`을 bounded하게 닫는 것이다.
- state별(stage 내 세부 상태) archive를 따로 만들면 오히려 큰 증거 묶음이 흩어진다.
- atlas/battle/oath 3개를 한 폴더에 합치면 다시 generic polish처럼 읽힌다.

---

## 3. stage별 starter bundle

## 3줄 요약
- stage마다 필수 evidence 종류는 이미 정의돼 있지만, bootstrap 시점에는 **어떤 파일을 먼저 예약해야 하는지**가 다르다.
- atlas는 route state와 threshold, battle은 slot과 hierarchy, oath는 role과 carry boundary가 먼저여야 한다.
- 아래 표는 `오늘 막 시작하는 stage`에서 최소 어떤 starter file set을 먼저 준비해야 하는지 고정한다.

### 한 줄 목표
stage마다 `첫 파일 4~6개`를 미리 예약해, review 시작 전에 이미 stage 언어가 흔들리지 않게 한다.

| stage | 먼저 만들 starter 파일 | 왜 이것부터 필요한가 |
| --- | --- | --- |
| `atlas-route-node` | `manifest.md`, `captures/atlas-1440-overview.png`, `notes/hook-manifest.md`, `notes/density-review.md`, `checks/dom-sanity.md`, `assets/starter-asset-list.md` | atlas는 route board가 실제로 보이는지와 hook/state 이름이 먼저 잠겨야 한다 |
| `battle-stakes-plaque` | `manifest.md`, `captures/battle-1440-overview.png`, `notes/hook-manifest.md`, `notes/density-review.md`, `checks/dom-sanity.md`, `assets/starter-asset-list.md` | battle은 current/next/grimoire slot contract와 first-glance 위계가 먼저다 |
| `oath-banner` | `manifest.md`, `captures/oath-1440-overview.png`, `notes/hook-manifest.md`, `notes/density-review.md`, `checks/dom-sanity.md`, `assets/starter-asset-list.md` | oath는 banner/result/carry role 분리와 CTA boundary가 starter 단계부터 있어야 한다 |

### stage별 추가 예약 파일

| stage | 권장 추가 starter 파일 | 이유 |
| --- | --- | --- |
| `atlas-route-node` | `captures/atlas-1440-threshold-focus.png`, `notes/handoff-line.md` | next threshold 읽힘과 archive-ready 문장을 분리해서 남기기 위해 |
| `battle-stakes-plaque` | `captures/battle-1440-stakes-focus.png`, `notes/handoff-line.md` | stakes 3슬롯 위계를 overview와 따로 검수하기 위해 |
| `oath-banner` | `captures/oath-1440-banner-focus.png`, `notes/handoff-line.md` | banner-first hierarchy와 carry 보조층을 별도 캡처로 잠그기 위해 |

---

## 4. owner drop rule

## 4줄 요약
- starter bundle은 `누가 뭘 만들지`보다 먼저 **누가 어디에 무엇을 처음 drop할지**를 고정해야 유지된다.
- 프로그래머는 hook/check 메모, UI는 overview/focus 캡처, 아트는 starter asset note, QA/PM은 handoff line 초안을 각 stage에 먼저 만든다.
- 내용이 짧아도 파일이 먼저 있어야 다음 사람이 같은 위치를 연다.
- starter 단계에서 generic 파일명을 허용하면 closing review가 다시 흐려진다.

### 한 줄 목표
첫 파일명부터 stage 책임을 말하게 만든다.

| 역할 | bootstrap 시점 최소 drop | 금지 |
| --- | --- | --- |
| 프로그래머 | `notes/hook-manifest.md`, `checks/dom-sanity.md` | `notes/dev.md`, `checks/check.txt` 같은 generic 이름 |
| UI | `captures/*-overview.png` 1개 또는 예약명 | `1.png`, `new.png`, `latest.png` |
| 아트 | `assets/starter-asset-list.md` | asset만 던지고 범위 설명 생략 |
| QA | `notes/handoff-line.md` 초안 또는 review note 1개 | 큰 green 문장을 먼저 기록 |
| PM | `manifest.md`의 `next allowed step`, `status`, `handoff line` 초안 | archive-ready 전 final wording 확정 |

### 권장 상태값
- `bootstrap-ready`
- `archive-in-progress`
- `archive-ready`

---

## 5. green 전 체크

## 3줄 요약
- archive가 존재한다고 바로 closing-ready는 아니다.
- `bootstrap-ready`와 `archive-ready`는 다른 상태다.
- 아래 체크를 통과해야 tracker/run log에서 surface closing 문장을 쓸 수 있다.

### 한 줄 목표
Run Table token 기록이 evidence보다 앞서지 않게 만든다.

| 상태 | 뜻 | 허용 행동 |
| --- | --- | --- |
| `bootstrap-ready` | 폴더/manifest/starter file 준비 완료 | refinement 시작 가능, closing 문장 금지 |
| `archive-in-progress` | 첫 evidence가 들어오고 starter file이 채워지는 중 | 중간 review 가능, closing 문장 금지 |
| `archive-ready` | required artifacts와 handoff line이 manifest 기준으로 채워짐 | tracker/run log closing 문장 가능 |

### archive-ready 직전 필수 체크
1. `manifest.md`에 `stage`, `date`, `status`, `required artifacts`, `next allowed step`, `handoff line`이 있다.
2. `captures/`, `notes/`, `checks/`, `assets/`가 모두 존재한다.
3. overview/focus 캡처 또는 예약명이 surface별 목적과 일치한다.
4. `hook-manifest.md`, `dom-sanity.md`, `starter-asset-list.md`가 있다.
5. generic 이름(`final`, `latest`, `misc`)이 없다.

---

## 6. 리스크 & 후속과제

| 리스크 | 영향 | 이번 대응 | 다음 확인 시점 |
| --- | --- | --- | --- |
| evidence manifest는 있어도 starter bundle이 없어 첫 refinement 턴에서 archive가 다시 비어 있는 위험 | 캡처와 메모가 임시 폴더/메신저로 새고 tracker/run log만 남을 수 있음 | stage별 starter file set과 bootstrap 순서를 packet + templates로 고정 | 다음 atlas/battle/oath refinement 착수 직전 |
| atlas/battle/oath 캡처 이름이 generic해서 stage boundary가 파일명부터 흐려지는 위험 | surface별 closing review가 다시 감상형으로 섞인다 | `overview/focus` 캡처 이름과 stage 폴더를 고정 | 첫 1440p review 직전 |
| starter asset 범위가 file-level로 안 잠기면 큰 자산 논의가 먼저 열리는 위험 | bounded token refinement가 art backlog로 돌아감 | `starter-asset-list.md`에 아직 금지 asset도 같이 적게 한다 | art sanity review 직전 |
| PM/QA가 bootstrap-ready와 archive-ready를 같은 초록불로 기록하는 위험 | half-closure가 다시 완료처럼 보인다 | manifest status와 handoff line 초안을 분리하고 archive-ready 전 final wording 금지 | tracker/run log 기록 직전 |

---

## 7. immediate next actions

1. 실제 refinement 턴을 열기 전에 `docs/artifacts/run-table-token-delivery/templates/`의 해당 stage starter manifest를 복사해 stage 폴더에 먼저 둔다.
2. atlas는 `threshold-focus`, battle은 `stakes-focus`, oath는 `banner-focus` 캡처 예약명부터 만든다.
3. 프로그래머는 hook manifest와 DOM sanity note를 먼저 채우고 나서 markup/CSS edit에 들어간다.
4. 아트는 starter asset list만 먼저 닫고 큰 배경/전체 shell 리뉴얼은 아직 열지 않는다.
5. QA/PM은 `bootstrap-ready`와 `archive-ready`를 구분해 기록한다.

---

## 바로 이어서 볼 문서
- `docs/dicespell_game_flow_token_evidence_manifest_kr.md`
- `docs/dicespell_game_flow_token_closing_packet_kr.md`
- `docs/dicespell_game_flow_token_gap_ledger_kr.md`
- `docs/dicespell_game_flow_token_source_of_truth_map_kr.md`

---

## 한 장 결론
지금 Run Table token 축의 남은 공백은 `무슨 evidence가 필요한가`가 아니라 **`그 evidence를 실제 첫 3분 안에 어떤 stage bundle로 열어야 다음 사람이 같은 위치에서 바로 검수할 수 있는가`**다. 이 문서는 atlas-route-node / battle-stakes-plaque / oath-banner 3단계 archive를 starter bundle과 bootstrap 순서, owner drop rule, green 전 체크까지 한 번 더 내려서, 다음 refinement가 다시 흩어진 캡처와 큰 green 문장으로 흐르지 않게 만드는 bounded archive bootstrap packet이다.
