# DiceSpell `overrunEvent` / 첫 런 온보딩 아카이브 패킷

## 3줄 요약
- 이 문서는 `증거 매니페스트`가 정의한 artifact를 **실제로 어디에 어떤 폴더 구조와 어떤 묶음 이름으로 보관할지** 고정하는 source-of-truth다.
- 지금 DiceSpell 문서는 `무엇을 제출해야 하는가`까지는 충분히 닫혔지만, 실제 handoff 직전에는 여전히 `좋아, 그 파일들을 어디에 어떻게 모아 두면 다음 사람과 다음 자동화가 안 헷갈리지?`가 마지막 운영 공백으로 남는다.
- 목표는 새 evidence를 더 만드는 것이 아니라, 이미 정의된 evidence를 **stage-first archive structure + bundle manifest + collision-safe naming**으로 다시 압축해 사람이든 자동화든 같은 위치에서 같은 묶음을 찾게 만드는 것이다.

**한 줄 목표:** `Commit 1 floor -> Commit 2 closing -> B-1/B-2/B-3/B-4`를 **stage별 archive 폴더 1개 + bundle manifest 1개 + owner drop rule 1세트**로 고정해 제출물이 handoff 직전 다시 흩어지지 않게 만든다.

---

## 왜 이 문서가 필요한가
- `증거 매니페스트`는 무엇을 제출해야 하는지까지는 고정했지만, 실제 작업 턴에서는 아래가 다시 흔들릴 수 있다.
  1. 파일명은 맞는데 저장 위치가 사람마다 달라 reviewer가 한 번 더 찾게 됨
  2. Commit 1/Commit 2/B-track evidence가 같은 임시 폴더에 섞여 boundary가 흐려짐
  3. 자동화 run log의 단계명과 실제 evidence 보관 위치가 어긋나 다음 턴에서 재활용이 어려워짐
  4. scene capture, smoke log, boundary note가 서로 다른 임시 경로에 흩어져 `handoff-ready` 판단이 다시 채팅 문맥 의존이 됨
- 이 패킷은 그 마지막 보관/회수 마찰을 줄이기 위해 만든다.

---

## 이 문서를 먼저 봐야 하는 사람
| 역할 | 먼저 볼 부분 | 바로 이어서 볼 문서 | 여기서 가져갈 결론 |
| --- | --- | --- | --- |
| 프로그래머 | `1. archive root`, `2. stage bundle 구조` | `증거 매니페스트`, `runtime handoff scorecard` | smoke/state diff를 어디에 넣고 어떤 파일이 빠지면 bundle 미완성인지 안다 |
| UI | `2. stage bundle 구조`, `4. capture drop rule` | `A-3 UI packet`, `visual asset packet` | scene/primer 캡처를 Commit 2와 B-track surface로 섞지 않는다 |
| 아트 | `4. capture drop rule`, `5. owner inbox/outbox` | `visual asset packet`, `content deck` | visual sanity note와 새 자산 제안은 서로 다른 묶음으로 남긴다 |
| QA | `2. stage bundle 구조`, `6. review pickup flow` | `execution reporting packet`, `증거 매니페스트` | stage manifest와 archive 폴더가 같은 단계명으로 닫혀야 green이다 |
| PM/기획 | `0. first screen 결론`, `7. green 직전 체크` | `readiness board`, `owner handoff packet` | tracker/run log 문장을 쓰기 전에 archive bundle이 꽉 찼는지 먼저 본다 |
| 자동화 writer | `3. collision-safe naming`, `8. append-only 운영 규칙` | `automation run log`, `execution reporting packet` | 새 턴 evidence는 append-only로 새 stage bundle에만 넣고 기존 bundle을 덮어쓰지 않는다 |

---

## 0. first screen 결론

### 이 문서가 답하는 질문
- `commit1-smoke-log.txt` 같은 파일을 실제로 어디에 두지?
- Commit 2 scene capture와 B-4 ending helper capture가 섞이지 않게 폴더를 어떻게 나누지?
- handoff-ready를 주장하기 전에 reviewer가 어떤 경로 하나만 열면 되는 상태를 어떻게 만들지?

### 가장 짧은 답
- 모든 evidence는 `docs/artifacts/<date>/<stage>/` 아래에 둔다.
- stage 폴더에는 `manifest.md`, `notes/`, `captures/`, `logs/` 4개 하위 구조를 기본으로 둔다.
- Commit 1/Commit 2/B-1/B-2/B-3/B-4는 **각자 다른 stage 폴더**를 갖고, 한 stage 폴더 안에서도 owner별 drop rule을 유지한다.
- green 문장은 archive manifest가 먼저 채워진 뒤에만 tracker/run log에 올라간다.

---

## 1. archive root

## 1-1. 권장 루트
- 권장 archive root: `docs/artifacts/`
- 날짜별 루트: `docs/artifacts/YYYYMMDD/`
- stage별 루트: `docs/artifacts/YYYYMMDD/<stage>/`

예시
```text
docs/artifacts/
  20260318/
    commit1-floor/
    commit2-closing/
    b1-library/
    b2-battle/
```

## 1-2. 왜 docs/artifacts 아래인가
- tracker/index/run log와 같은 저장소 안에서 상대 경로로 참조하기 쉽다.
- 외부 배포 포털에서는 링크 대상이 될 수 있지만, 실제 목적은 `handoff-ready evidence의 동일 경로 보관`이다.
- 코드/문서 변경과 같은 git history 안에서 evidence 존재 여부를 함께 추적하기 쉽다.

## 1-3. 금지 루트
- `Desktop/`, `Downloads/`, 개인 temp 폴더, 메시지 첨부함 같은 저장소 밖 경로
- `docs/` 최상단에 loose file로 바로 두기
- stage 이름 없이 날짜 폴더 하나에 전부 넣기

---

## 2. stage bundle 구조

## 공통 폴더 구조
```text
<stage>/
  manifest.md
  logs/
  captures/
  notes/
```

### 각 폴더 용도
| 폴더 | 용도 | 예시 |
| --- | --- | --- |
| `manifest.md` | stage 전체 제출물 체크와 최종 handoff 문장 | `commit1 manifest`, `b3 manifest` |
| `logs/` | smoke/acceptance/CLI 출력 | `20260318-commit1-qa-smoke-log.txt` |
| `captures/` | UI/아트/상태 시각 증거 | `20260318-commit2-ui-theme-scene.png` |
| `notes/` | state diff, boundary note, non-change note, recovery note | `20260318-commit1-programmer-state-diff.md` |

### stage별 기본 묶음
| stage | 필수 하위 증거 | 최소 목표 |
| --- | --- | --- |
| `commit1-floor` | smoke log, state diff, non-change note, payload sanity | enter/snapshot/recovery floor가 archive로 재현 가능해야 함 |
| `commit2-closing` | smoke log, resolve diff, scene captures, boundary note | scene/resolve/baseline 제거가 같은 stage로 묶여야 함 |
| `b1-library` | surface capture, recovery note, handoff line | library primer만 단독 surface로 읽혀야 함 |
| `b2-battle` | surface capture, first action note, handoff line | battle hint trigger가 별도 묶음으로 남아야 함 |
| `b3-reward-shop` | reward capture, shop capture, boundary note | reward와 shop guidance를 따로 검수할 수 있어야 함 |
| `b4-ending` | ending capture, boundary note, handoff line | ending helper와 overrun flavor 경계를 같이 검수해야 함 |

---

## 3. collision-safe naming

## 기본 형식
`YYYYMMDD-stage-owner-artifact.ext`

예시
- `20260318-commit1-programmer-state-diff.md`
- `20260318-commit2-ui-theme-scene.png`
- `20260318-b4-pm-boundary-note.md`

## 같은 날 여러 번 갱신될 때
- 기존 파일을 덮어쓰기보다 아래 둘 중 하나를 쓴다.
  1. `YYYYMMDD-stage-owner-artifact-v2.ext`
  2. `YYYYMMDD-stage-owner-artifact-1430.ext`
- `latest`, `final-final`, `real-final` 같은 이름은 금지한다.

## stage 폴더 이름도 고정한다
| stage 문장 | 폴더명 |
| --- | --- |
| Commit 1 floor | `commit1-floor` |
| Commit 2 closing | `commit2-closing` |
| B-1 library | `b1-library` |
| B-2 battle | `b2-battle` |
| B-3 reward/shop | `b3-reward-shop` |
| B-4 ending | `b4-ending` |

---

## 4. capture drop rule

## Commit 2 scene capture
- `captures/` 안에 branch별 파일을 따로 둔다.
- 권장 세트
  - `theme-scene`
  - `enemy-scene`
  - `none-scene`
- `ending` helper 캡처는 Commit 2 폴더에 두지 않는다.

## B-track capture
- B-1: library hero-primer 전체 surface
- B-2: battle entry / first-spell / auto-pass 중 실제 검수 대상 캡처
- B-3: reward와 shop을 각각 다른 파일로 둔다
- B-4: ending helper만 둔다. `themeTrophy` / `enemyTrophy` / `none` flavor scene은 Commit 2가 가진다.

## 금지 패턴
- 한 PNG 안에 Commit 2와 B-4를 콜라주로 섞기
- capture 폴더에 state diff 문서를 같이 두기
- reward/shop을 같은 `shop-reward.png`로 뭉개기

---

## 5. owner inbox / outbox 규칙

## owner별 drop rule
| 역할 | 기본 drop 위치 | 비고 |
| --- | --- | --- |
| 프로그래머 | `logs/`, `notes/` | smoke log + state/resolve diff 중심 |
| UI | `captures/`, `notes/` | hierarchy/boundary 메모는 `notes/` |
| 아트 | `captures/`, `notes/` | token/accent sanity는 note로 남긴다 |
| QA | `logs/`, `notes/` | acceptance 번호가 들어간 로그 우선 |
| PM/기획 | `manifest.md`, `notes/` | handoff 문장과 다음 허용 단계 명시 |

## outbox 원칙
- handoff-ready 공유는 stage 폴더 경로 하나로 한다.
- 메시지/리뷰 본문에는 파일을 다 나열하기보다 아래만 남긴다.
  - stage명
  - archive 경로
  - manifest 결론 1줄

예시
> `Commit 1 floor evidence archive: docs/artifacts/20260318/commit1-floor/`  
> `manifest 기준 필수 제출물 완료, reward apply/page advance/onboarding 미오픈 유지 확인.`

---

## 6. review pickup flow

### reviewer가 여는 순서
1. `manifest.md`
2. `logs/`의 smoke/acceptance 출력
3. `notes/`의 non-change 또는 boundary note
4. `captures/`의 surface sanity

### 왜 이 순서인가
- manifest 없이 캡처부터 보면 stage가 섞였는지 먼저 알기 어렵다.
- smoke/acceptance 번호를 먼저 확인해야 `보였다`와 `동작했다`를 분리해서 읽을 수 있다.
- boundary/non-change note를 확인해야 half-cutover를 막을 수 있다.

---

## 7. green 직전 체크

아래를 모두 만족해야 tracker/run log green 문장을 쓴다.
1. stage archive 폴더가 있다
2. `manifest.md`가 있다
3. 필수 artifact가 폴더 구조에 맞게 분리돼 있다
4. logs/captures/notes 중 하나라도 비어 있으면 그 stage는 미완성으로 본다
5. 다음 허용 단계가 manifest와 tracker/run log에서 같은 문장이다

### green 문장 예시
- `Commit 1 floor archive-ready: docs/artifacts/20260318/commit1-floor/ manifest 기준 필수 제출물 완료.`
- `Commit 2 closing archive-ready: docs/artifacts/20260318/commit2-closing/ resolve/scene/boundary evidence bundle 완료.`
- `B-3 reward/shop archive-ready: docs/artifacts/20260318/b3-reward-shop/ reward/shop 분리 primer evidence 완료.`

---

## 8. append-only 운영 규칙
- 같은 stage의 기존 evidence를 큰 덩어리로 재작성하지 않는다.
- stage가 다시 열리면 `v2` 또는 시각 suffix를 붙인 새 artifact를 추가하고, `manifest.md`에 superseded note만 남긴다.
- run log는 stage 완료를 append한다. archive 폴더 정정 때문에 옛 entry를 대규모 수정하지 않는다.
- 자동화 writer는 green 문장을 쓰기 전에 archive 경로를 먼저 확인하고, 없으면 green 대신 blocker note를 남긴다.

---

## 9. 권장 manifest 템플릿

```md
# <stage> manifest

- stage: commit1-floor
- date: 2026-03-18
- owner summary:
  - programmer:
  - ui:
  - art:
  - qa:
  - pm:

## required artifacts
- [ ] logs/...smoke-log.txt
- [ ] notes/...state-diff.md
- [ ] notes/...nonchange-note.md
- [ ] captures/...payload-sanity.png
- [ ] handoff line confirmed

## boundary / non-change
- 

## next allowed step
- 

## handoff line
> 
```

---

## 10. 리스크 & 후속과제
- 가장 큰 리스크는 evidence manifest가 생긴 뒤에도 실제 파일이 저장소 밖 개인 임시 폴더에 머물러, tracker/run log 문장과 archive 경로가 분리되는 것이다.
- 두 번째 리스크는 stage 폴더는 나눴는데도 `manifest.md`가 비어 있어 reviewer가 다시 파일을 수동 조합해야 하는 것이다.
- 세 번째 리스크는 Commit 2 scene capture와 B-4 ending helper capture를 같은 `captures/` 묶음으로 두어 boundary 문서가 다시 약해지는 것이다.
- 따라서 다음 writer 구현 슬롯은 새 설계보다 먼저 **archive root + stage 폴더 + manifest 파일**부터 만들고 evidence를 drop해야 한다.
- 후속으로 필요하다면 다음 문서화 슬롯은 `manifest 템플릿 자동 생성 스크립트` 또는 `archive cleanup/superseded 규칙` 수준으로만 더 좁혀야 한다. 범위를 다시 문서 관리 시스템 전체로 키우면 안 된다.

---

## 11. immediate next actions
1. 실제 A-track 구현 턴을 열면 `docs/artifacts/YYYYMMDD/commit1-floor/`부터 만든다.
2. Commit 2에서는 scene capture와 boundary note를 `commit2-closing` 폴더에만 둔다.
3. B-track을 열게 되면 `surface 1개 = stage 폴더 1개` 원칙을 그대로 재사용한다.
4. tracker/run log green 문장에는 archive 경로를 함께 남긴다.
