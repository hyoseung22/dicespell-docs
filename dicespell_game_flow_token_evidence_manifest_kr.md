# DiceSpell `Run Table` Token Evidence Manifest Packet

## 3줄 요약
- 이 문서는 `surface handoff packet` → `token anchor packet` → `token delivery workboard` → `token closing packet` 다음에 남아 있던 마지막 실무 공백인 **실제 제출 파일을 무엇으로 묶고 어떻게 이름 붙일지**를 고정한다.
- 읽는 대상은 프로그래머, UI 오너, 아트 오너, QA/PM이다. 새 화면 제안이 아니라 `Run Atlas / Battle Table / Overrun Oath` 3면의 **evidence bundle 계약**을 source-of-truth로 잠그는 문서다.
- source-of-truth는 이 문서, 빠른 브라우징용 companion은 `docs/dicespell_game_flow_token_evidence_manifest_summary_kr.md`, 선행 계약은 `docs/dicespell_game_flow_token_closing_packet_kr.md`, `docs/dicespell_game_flow_token_delivery_workboard_kr.md`, `docs/dicespell_game_flow_token_anchor_packet_kr.md`다.

## 한 줄 목표
`route node / stakes plaque / oath banner` closing을 `캡처 몇 장 있음` 수준으로 남기지 않고, **어떤 파일 묶음이 있어야 handoff-ready라고 말할 수 있는지**를 stage/file 수준으로 고정한다.

## 왜 이 문서가 필요한가
지금까지 Run Table 문서 묶음은 이미 충분히 다음을 잠갔다.

- 어떤 surface가 무엇을 맡는가 (`surface handoff`)
- 어떤 token과 DOM/QA anchor를 먼저 잠가야 하는가 (`token anchor`)
- 누가 어느 함수/스타일부터 시작해야 하는가 (`token delivery workboard`)
- 무엇이 제출되면 closing이라고 말할 수 있는가 (`token closing packet`)

하지만 실제 refinement 직전에는 아직 아래 공백이 남아 있다.

- 프로그래머는 hook 목록을 만들 수 있지만, **그 hook을 어떤 파일 이름/섹션 이름으로 남겨야 리뷰가 빨라지는지**가 없다.
- UI 오너는 1440p 캡처를 만들 수 있지만, **atlas/battle/oath 캡처가 어떤 비교쌍과 어떤 제목으로 저장돼야 하는지**가 없다.
- 아트 오너는 starter asset 3묶음을 낼 수 있지만, **이번 턴 제출 범위가 무엇인지와 무엇을 아직 내면 안 되는지**가 파일 수준으로 잠기지 않았다.
- QA/PM은 closing sentence를 적을 수 있지만, **capture / hook / review / asset note가 어느 폴더 구조로 있어야 다음 사람이 같은 위치에서 바로 찾는지**가 없다.

즉 지금 남은 병목은 `무엇이 pass인가`에서 한 단계 더 내려간 **`그 pass를 어떤 파일 묶음으로 제출하는가`**다. 이 문서는 그 제출 단위를 고정한다.

---

## 먼저 무엇을 읽어야 하는가

| 역할 | 먼저 볼 문서 | 그 다음 | 이 문서에서 특히 볼 섹션 |
| --- | --- | --- | --- |
| 프로그래머 | `docs/dicespell_game_flow_token_closing_packet_kr.md` | 이 문서 | 2, 3, 5, 8 |
| UI 오너 | `docs/dicespell_game_flow_token_closing_summary_kr.md` | 이 문서 | 3, 4, 6, 8 |
| 아트 오너 | summary | 이 문서 | 4, 6, 7 |
| QA / PM | summary | 이 문서 | 5, 6, 8, 9 |

---

## 1. 범위 선언

### 이번 문서가 다루는 것
- `Run Atlas` / `Battle Table` / `Overrun Oath` closing evidence의 권장 폴더 구조
- `hook lock / density lock / starter asset lock` 단계별 제출 파일 이름
- owner별 제출 책임과 금지 파일
- tracker/run log용 handoff 문장과 매칭되는 evidence manifest 규칙

### 이번 문서가 의도적으로 다루지 않는 것
- 새 phase 정의
- 새 token 설계
- `overrunEvent` / 온보딩 artifact 체계 대체
- 큰 PSD/원본 아트 파이프라인 규정
- git LFS 정책이나 외부 저장소 규칙

### 이번 단계의 핵심 판단
다음 refinement의 진짜 리스크는 더 이상 `문서가 없다`가 아니다. 이제 리스크는 **evidence가 있어도 흩어져 있어 closing review가 다시 감상형으로 돌아가는 것**이다. 따라서 이번 단계는 새 화면보다 **파일 구조와 파일명**을 먼저 잠그는 쪽이 값이 크다.

---

## 2. Core structure: evidence bundle을 4묶음으로만 본다

| 묶음 | 무엇을 담는가 | 누가 주로 채우는가 | 왜 분리하는가 |
| --- | --- | --- | --- |
| `captures/` | 1440p 시각 증거 | UI, QA | first-glance 판정을 감상형 코멘트와 분리하기 위해 |
| `notes/` | hook manifest, density review, handoff line | 프로그래머, UI, PM | 캡처만으로는 DOM/state 계약을 보존할 수 없어서 |
| `assets/` | starter asset 범위와 파일 목록 | 아트 | token closing과 art backlog를 분리하기 위해 |
| `checks/` | selector/DOM sanity, 간단한 검증 메모 | 프로그래머, QA | `있어 보임`과 `실제로 hook이 있음`을 분리하기 위해 |

### 권장 archive root
- 로컬 작업/노션 붙여넣기 기준 source-of-truth: `docs/artifacts/run-table-token-delivery/<stage>/`
- `<stage>`는 아래 셋 중 하나만 허용한다.
  - `atlas-route-node`
  - `battle-stakes-plaque`
  - `oath-banner`

### 왜 stage를 3개로 고정하는가
- atlas/battle/oath를 한 폴더에 같이 모으면 surface drift가 리뷰에서 늦게 보인다.
- hook/density/asset을 한 파일에 다 때려 넣으면 누가 무엇을 제출했는지 흐려진다.
- 이번 refinement는 3면만 bounded하게 닫는 것이 목표이므로, archive도 3면보다 넓어지면 안 된다.

---

## 3. Surface별 manifest 템플릿

## 3-1. `atlas-route-node`

### 왜 이 stage가 따로 필요한가
`Run Atlas`는 preview/log panel과 쉽게 섞인다. 따라서 route node closing 증거는 `atlas`만 따로 모아야 `current / past / shop / boss / upcoming` 읽힘이 정말 잠겼는지 빠르게 판정할 수 있다.

### 필수 파일
| 경로 | 필수 내용 | owner |
| --- | --- | --- |
| `captures/atlas-1440-overview.png` | route board 전체 1440p 캡처 | UI |
| `captures/atlas-1440-threshold-focus.png` | current node + next threshold 읽힘 캡처 | UI |
| `notes/hook-manifest.md` | `data-run-surface`, `data-route-node-state`, `data-route-threshold` 정리 | 프로그래머 |
| `notes/density-review.md` | `유지 / 약함 / 과함` 기준 3~5줄 | UI |
| `notes/handoff-line.md` | tracker/run log 복붙용 1줄 | PM/QA |
| `checks/dom-sanity.md` | atlas root/state hook 존재 확인 메모 | 프로그래머/QA |
| `assets/starter-asset-list.md` | Route Node Starter 범위와 아직 미제출 항목 | 아트 |

### 좋은 handoff line 예시
- `Run Atlas route node는 hook/state/threshold 증거와 1440p density 캡처가 같은 stage archive에 정리되어 closing review 가능 상태다.`

### 금지
- `final.png`, `latest.png`, `misc-notes.md` 같은 generic 파일명
- atlas/battle/oath를 한 `captures/` 폴더에 섞어 저장
- boss/shop 상태를 텍스트 설명만으로 남기고 캡처를 빼는 것

---

## 3-2. `battle-stakes-plaque`

### 왜 이 stage가 따로 필요한가
stakes 3카드는 현재 battle header extension이라는 게 핵심이다. 따라서 전투 메인 HUD 전체를 다 캡처하는 대신, `current / next / grimoire`가 실제 plaque 역할로 읽히는 증거만 따로 모아야 한다.

### 필수 파일
| 경로 | 필수 내용 | owner |
| --- | --- | --- |
| `captures/battle-1440-overview.png` | battle surface 전체 1440p 캡처 | UI |
| `captures/battle-1440-stakes-focus.png` | stakes 3카드 위계 집중 캡처 | UI |
| `notes/hook-manifest.md` | `data-run-surface`, `data-stakes-slot` 정리 | 프로그래머 |
| `notes/density-review.md` | current > next > grimoire 읽힘 3~5줄 | UI |
| `notes/handoff-line.md` | tracker/run log 복붙용 1줄 | PM/QA |
| `checks/dom-sanity.md` | battle slot hook 존재와 slot naming 확인 | 프로그래머/QA |
| `assets/starter-asset-list.md` | Stakes Plaque Starter 범위와 미제출 항목 | 아트 |

### 좋은 handoff line 예시
- `Battle Table stakes plaque는 slot hook, 1440p hierarchy 캡처, starter frame 목록까지 같은 archive에서 검수 가능한 상태다.`

### 금지
- 주사위/몬스터 전장 전체 리페인트를 closing asset처럼 제출
- `current/next/grimoire` 구분 없이 `stakes.png` 한 장만 제출
- density review 없이 캡처만 던지고 `보면 앎`으로 끝내기

---

## 3-3. `oath-banner`

### 왜 이 stage가 따로 필요한가
ending은 reward/shop family와 가장 쉽게 섞인다. 따라서 `banner / result / carry` 역할 분리 증거와 starter asset 증거를 같은 stage archive로 묶어야 `문턱 장면` 읽힘이 살아 있는지 바로 판정할 수 있다.

### 필수 파일
| 경로 | 필수 내용 | owner |
| --- | --- | --- |
| `captures/oath-1440-overview.png` | ending surface 전체 1440p 캡처 | UI |
| `captures/oath-1440-banner-focus.png` | banner 우선순위 + carry 보조층 집중 캡처 | UI |
| `notes/hook-manifest.md` | `data-run-surface`, `data-oath-role`, `data-overrun-cta` 정리 | 프로그래머 |
| `notes/density-review.md` | banner/result/carry 위계 코멘트 3~5줄 | UI |
| `notes/handoff-line.md` | tracker/run log 복붙용 1줄 | PM/QA |
| `checks/dom-sanity.md` | oath role hook 존재와 CTA 분리 확인 | 프로그래머/QA |
| `assets/starter-asset-list.md` | Oath Banner Starter 범위와 미제출 항목 | 아트 |

### 좋은 handoff line 예시
- `Overrun Oath banner는 role hook, banner-first 1440p 캡처, starter asset 범위가 같은 stage archive로 묶여 reward/shop drift 없이 closing review 가능 상태다.`

### 금지
- ending helper와 `overrunEvent` scene card를 같은 asset family처럼 제출
- main banner보다 side card가 더 강하게 보이는 캡처만 남기고 보완 메모를 생략
- carry-forward 문맥을 `summary note` 하나로만 설명하고 role hook/캡처를 빼는 것

---

## 4. Role-specific handoff points

## 4-1. 프로그래머에게 넘길 것

### 제출 파일
- 각 stage의 `notes/hook-manifest.md`
- 각 stage의 `checks/dom-sanity.md`

### 최소 작성 규칙
- `hook-manifest.md`는 아래 4항만 있으면 된다.
  1. root hook
  2. state/slot/role hook
  3. 관련 함수 anchor
  4. 아직 안 여는 범위
- `dom-sanity.md`는 `존재함/없음`과 간단한 확인 방법 1~2줄만 남긴다.

### 금지
- helper 전체 설계를 장문으로 다시 쓰기
- class rename 전체 diff를 manifest에 붙이기
- atlas/battle/oath 외 surface hook까지 같이 확장하기

## 4-2. UI 오너에게 넘길 것

### 제출 파일
- 각 stage의 `captures/*overview.png`
- 각 stage의 `captures/*focus.png`
- 각 stage의 `notes/density-review.md`

### 최소 작성 규칙
- 캡처는 반드시 `overview`와 `focus` 2장으로 나눈다.
- review는 surface당 3~5줄을 넘기지 않는다.
- 각 줄은 반드시 `유지`, `약함`, `과함` 중 하나의 판정어를 포함한다.

### 금지
- 모바일/임의 해상도 캡처를 closing 기본 증거로 쓰기
- 캡처 파일명을 `1.png`, `new.png`처럼 저장
- visual 감상문을 길게 쓰고 focus 캡처를 생략하기

## 4-3. 아트 오너에게 넘길 것

### 제출 파일
- 각 stage의 `assets/starter-asset-list.md`
- 필요한 경우 starter asset preview 1~2개

### 최소 작성 규칙
- `starter-asset-list.md`는 아래 3항만 있으면 된다.
  1. 이번 턴 제출 asset
  2. 아직 금지 asset
  3. drift 방지 메모

### 금지
- 큰 배경, 전체 shell 리페인트, 개별 일러스트 발주를 starter asset처럼 제출
- atlas/battle/oath 공용 한 장으로 묶어 surface 차이를 흐리기

## 4-4. QA / PM에게 넘길 것

### 제출 파일
- 각 stage의 `notes/handoff-line.md`
- 선택적으로 `notes/review-decision.md`

### 최소 작성 규칙
- handoff line은 1문장만 허용한다.
- `거의`, `대체로`, `예쁨`, `좋아 보임` 같은 감상형 단어는 금지한다.
- closing packet의 단계어(`hook lock`, `density lock`, `starter asset lock`) 중 어디까지 닫혔는지 포함한다.

---

## 5. Stage archive 구조 예시

```text
docs/artifacts/run-table-token-delivery/
  atlas-route-node/
    captures/
      atlas-1440-overview.png
      atlas-1440-threshold-focus.png
    notes/
      hook-manifest.md
      density-review.md
      handoff-line.md
    checks/
      dom-sanity.md
    assets/
      starter-asset-list.md
  battle-stakes-plaque/
    ...
  oath-banner/
    ...
```

### 왜 이 구조를 권장하는가
- review어가 surface 하나만 빠르게 열 수 있다.
- owner별 파일 위치가 고정돼 재요청이 줄어든다.
- 노션/컨플에 옮길 때도 `captures / notes / assets / checks` 4묶음으로 바로 복사 가능하다.

---

## 6. Acceptance / fail matrix

| 체크 | 기대값 | fail 예시 | 판정 |
| --- | --- | --- | --- |
| stage 분리 | atlas/battle/oath가 각자 archive를 가진다 | 한 폴더에 전부 섞여 있음 | fail |
| capture 쌍 | overview + focus 2장씩 있다 | 전체 화면 1장뿐 | fail |
| hook manifest | root/state/slot/role와 함수 anchor가 있다 | `hook 넣음` 한 줄뿐 | fail |
| density review | `유지/약함/과함` 판정어가 있다 | `좋아 보임`만 있음 | fail |
| starter asset list | 제출/금지/drift 메모가 있다 | asset 파일만 있고 설명 없음 | fail |
| handoff line | tracker/run log 복붙 문장 1개가 있다 | 길고 애매한 진행 보고만 있음 | fail |

---

## 7. 리스크 & 후속과제

| 리스크 | 영향 | 이번 문서 기준 대응 |
| --- | --- | --- |
| closing packet은 있어도 파일 구조가 없으면 evidence가 메시지/스크린샷 DM에 흩어진다 | 다음 reviewer가 같은 증거를 다시 수집해야 한다 | stage별 archive root와 필수 파일 이름을 먼저 고정한다 |
| atlas/battle/oath를 한 묶음으로 저장하면 surface drift가 늦게 보인다 | bounded refinement가 generic polish처럼 기록된다 | stage를 3개로 분리하고 surface별 handoff line을 따로 남긴다 |
| starter asset 범위가 파일 수준으로 잠기지 않으면 큰 배경부터 열릴 수 있다 | closing이 아니라 art backlog가 열린다 | `starter-asset-list.md`에 금지 asset도 같이 적게 한다 |
| review 문장이 캡처 파일명과 연결되지 않으면 tracker/run log 복붙 시 과장된다 | `거의 완료` 같은 애매한 종료선이 되살아난다 | `handoff-line.md`를 별도 파일로 두고 한 문장만 허용한다 |

---

## 8. Immediate next actions

1. `token closing packet -> token evidence manifest packet`을 Run Table closing 스택으로 함께 읽는다.
2. `docs/artifacts/run-table-token-delivery/` 아래에 `atlas-route-node`, `battle-stakes-plaque`, `oath-banner` 3 stage archive를 먼저 만든다.
3. 프로그래머는 각 stage에 `hook-manifest.md`와 `dom-sanity.md`를 먼저 채운다.
4. UI 오너는 surface당 `overview/focus` 캡처 2장과 density review 1개만 남긴다.
5. 아트 오너는 starter asset list만 먼저 제출하고, 큰 배경/전체 리페인트는 아직 열지 않는다.
6. QA/PM은 `handoff-line.md` 문장을 tracker/run log/public portal 문장으로 그대로 재사용한다.

---

## 9. 한 장 결론
지금 남은 공백은 더 이상 `무엇이 closing인가`가 아니다. 이제 남은 공백은 **`그 closing을 실제 어떤 파일 묶음으로 제출해야 다음 사람이 같은 위치에서 바로 검수할 수 있는가`**다. 이 문서는 atlas/battle/oath 3면의 closing evidence를 stage archive, 파일 이름, owner 제출물, handoff 문장까지 한 번 더 내려서, Run Table 후속 refinement가 다시 흩어진 캡처와 애매한 완료 문장으로 흐르지 않게 만드는 bounded evidence manifest packet이다.
