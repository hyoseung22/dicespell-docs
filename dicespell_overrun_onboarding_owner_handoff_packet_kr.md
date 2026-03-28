# DiceSpell `overrunEvent` → 첫 런 온보딩 오너 핸드오프 패킷

## 3줄 요약
- 이 문서는 `A-track Commit 1/2`와 `B-track B-1~B-4`를 **오너별 실제 전달 묶음**으로 다시 압축한 source-of-truth다.
- 프로그래머는 `이번 커밋에 어떤 파일/문서/증거를 같이 넘기는가`, UI/아트는 `언제 검수만 하고 언제 실제 마감을 여는가`, PM/QA는 `어떤 green 문장과 증거를 같이 잠그는가`를 먼저 보면 된다.
- 핵심은 새 설계를 더하는 것이 아니라, 이미 닫힌 stack / scorecard / owner workboard를 **사람 handoff 패킷**으로 재조합해 문서가 충분한 상태에서도 전달 언어가 다시 흐려지지 않게 만드는 것이다.

## 한 줄 목표
각 오너가 `지금 읽어야 할 문서`, `이번 턴 산출물`, `넘기면 안 되는 범위`, `tracker/run log 문장`을 한 화면에서 바로 가져가게 만든다.

## 왜 이 문서가 지금 필요한가
현재 문서 세트는 이미 충분히 세다.

- A-track에는 `runtime patch map`, `first/second runtime packet`, `commit stack`, `handoff scorecard`, `owner workboard`가 있다.
- B-track에도 `runtime stack`, `handoff scorecard`, `owner workboard`, `B-1~B-4 packet`이 있다.
- cross-track 차원에서도 `cutover master plan`이 실행 큐를 정리한다.

그런데 실제 handoff 순간에는 여전히 작은 공백이 남는다.

- 프로그래머에게 지금 이 턴에 어떤 문서 묶음을 보내야 하지?
- UI는 이번 턴이 검수 턴인가, 실제 마감 턴인가?
- 아트는 지금 token/accent sanity만 보면 되나, 실제 패키지를 닫아도 되나?
- PM/QA는 어떤 evidence bundle과 어떤 green 문장을 같이 잠가야 하나?

이 문서는 그 마지막 전달 공백을 메운다.

## 이 문서를 누가 어떻게 읽어야 하나
| 역할 | 먼저 볼 부분 | 바로 가져갈 결론 |
| --- | --- | --- |
| 프로그래머 | `1. 오너별 즉시 전달 묶음`, `2. 단계별 handoff 표` | 이번 커밋에 어떤 packet/scorecard/workboard를 같이 열고 어떤 파일 경계까지만 닫는지 고정한다 |
| UI | `1. 오너별 즉시 전달 묶음`, `3. UI/아트 마감 타이밍` | 지금이 layout sanity 턴인지, 실제 readable closing 턴인지 구분한다 |
| 아트 | `1. 오너별 즉시 전달 묶음`, `3. UI/아트 마감 타이밍` | 새 대형 자산 턴인지, token/accent/shell drift 제어 턴인지 구분한다 |
| QA | `2. 단계별 handoff 표`, `4. 제출 증거 규칙` | commit/surface별 증거 묶음과 금지 문장을 다시 섞지 않게 만든다 |
| PM/기획 | `0. first screen 결론`, `2. 단계별 handoff 표`, `5. 리스크 & 후속과제` | 각 단계의 완료 문장을 오너 handoff와 같은 언어로 기록한다 |

## 0. first screen 결론
### 이 문서를 왜 열어야 하나
- 사람 handoff를 `track 설명`이 아니라 `실제 전달 묶음`으로 고정하기 위해 연다.
- 각 오너가 `지금은 무엇을 제출하고, 무엇은 아직 제출하면 안 되는가`를 다시 추론하지 않게 하기 위해 연다.
- tracker/run log 문장과 owner handoff 문장을 같은 언어로 맞추기 위해 연다.

### 이번 문서의 판정 규칙
- Commit 1에서는 프로그래머/QA/PM handoff까지만 green이다. UI/아트는 검수 메모만 남긴다.
- Commit 2에서만 UI/아트 closing handoff가 열린다.
- B-track은 반드시 `B-1 -> B-2 -> B-3 -> B-4` 순서이며, 각 surface는 `surface 1개 = commit 1개 = owner handoff 1개`로만 닫는다.
- B-4에서도 `ending`은 결과/CTA 의미만 맡고, flavor는 끝까지 `overrunEvent`에 남긴다.

## 1. 오너별 즉시 전달 묶음

### 1-1. 프로그래머에게 지금 건네는 묶음
| 단계 | 먼저 전달할 문서 | 같이 잠가야 할 파일 경계 | 이번 턴 산출물 |
| --- | --- | --- | --- |
| Commit 1 | `runtime patch map` → `first runtime delivery packet` → `runtime commit stack` → `runtime handoff scorecard` | `src/game-engine.js`, `scripts/smoke-test.mjs` 중심. 필요 시 `src/app.js`는 진입 연결만 허용 | `enter wrapper`, `canonical snapshot floor`, `normalize/recovery`, `non-change line evidence` |
| Commit 2 | `second runtime slice` → `second runtime review` → `runtime commit stack` → `runtime owner workboard` | `src/app.js`, `src/styles.css`, `src/game-engine.js`, `scripts/smoke-test.mjs` | `scene card`, `confirm resolve`, `resolved guard`, `old immediate baseline 제거` |
| B-1 | `onboarding runtime stack` → `handoff scorecard` → `owner workboard` → `B-1 packet` | `src/app.js`, `src/game-engine.js` 또는 meta 저장 훅 | `seenBookSelectPrimer`, hero-primer, 비차단 선택 흐름 |
| B-2 | 위 순서 + `B-2 packet` | `src/app.js` battle surface, 관련 trigger hook | `battle.entry`, `first-spell`, `auto-pass` primer |
| B-3 | 위 순서 + `B-3 packet` | `src/app.js` reward/shop surface | `reward.first`, `shop.first`, 목적 분리 유지 |
| B-4 | 위 순서 + `B-4 packet` + `ending_overrun_boundary_packet` | `src/app.js` ending surface | `ending.defeat`, `ending.victory`, `ending.overrun-cta`, boundary 유지 |

### 1-2. UI에게 지금 건네는 묶음
| 단계 | 먼저 전달할 문서 | 이번 턴에 UI가 해야 할 일 | 아직 하면 안 되는 일 |
| --- | --- | --- | --- |
| Commit 1 | `a3_ui_packet` 참고 + `runtime owner workboard` | payload 표정 sanity, `none` branch 밀도 메모 | shell 최종 마감, ending과 거의 같은 표정으로 수렴시키기 |
| Commit 2 | `a3_ui_packet` + `second runtime slice` + `second runtime review` | 중앙 카드 위계, CTA readable sanity, ending과의 시각 경계 closing | onboarding primer를 같이 마감하기 |
| B-1 | `screen packet` + `B-1 packet` + `owner workboard` | library hero-primer 위치와 하이라이트 밀도 sanity | battle/reward/shop/ending primer까지 같이 여는 일 |
| B-2 | `screen packet` + `B-2 packet` | battle hero/inline hint 밀도 sanity | 전투 레이아웃 자체를 다시 설계하기 |
| B-3 | `screen packet` + `B-3 packet` + `content deck` | reward vs shop 목적 차이 readable closing | reward/shop을 같은 badge/accent strip으로 수렴시키기 |
| B-4 | `screen packet` + `B-4 packet` + `boundary packet` | ending helper의 절제된 톤 closing | overrun flavor를 ending helper에 흡수하기 |

### 1-3. 아트에게 지금 건네는 묶음
| 단계 | 먼저 전달할 문서 | 이번 턴에 아트가 해야 할 일 | 아직 하면 안 되는 일 |
| --- | --- | --- | --- |
| Commit 1 | `event_screen_packet`, `event_content_deck`, `runtime owner workboard` | token/accent/backplate sanity 메모 | 대형 신규 key art 제작을 선행 목표로 열기 |
| Commit 2 | 위 문서 + `second runtime slice` | 장면 카드 tone, badge/accent drift closing | onboarding primer까지 같은 시각 언어로 합치기 |
| B-1/B-2 | `production cutsheet`, `content deck`, `owner workboard` | primer token/accent 최소 세트 유지 | surface 여러 개를 한 번에 마감하기 |
| B-3 | `content deck`, `B-3 packet`, `owner workboard` | reward/shop 구분 토큰 마감 | shop을 reward와 같은 기대감 카드로 만들기 |
| B-4 | `content deck`, `B-4 packet`, `boundary packet` | ending은 절제된 강조, overrun은 장면 강조 유지 | ending과 overrunEvent를 같은 카드 표정으로 수렴시키기 |

### 1-4. QA에게 지금 건네는 묶음
| 단계 | 먼저 전달할 문서 | 이번 턴 산출물 | 금지되는 판정 |
| --- | --- | --- | --- |
| Commit 1 | `first runtime review`, `runtime handoff scorecard`, `runtime owner workboard` | smoke, 상태 diff, non-change line pass/fail | Commit 1을 runtime-ready처럼 pass 처리 |
| Commit 2 | `second runtime review`, `runtime handoff scorecard`, `runtime owner workboard` | smoke migration, UI/DOM evidence, resolve 1회 적용 pass/fail | `보인다`를 `runtime-ready`로 과장 |
| B-1~B-4 | `acceptance matrix`, `onboarding handoff scorecard`, `onboarding owner workboard`, 해당 B packet | surface별 첫 노출/재노출/비차단/경계 유지 pass/fail | surface 여러 개를 같은 evidence bundle로 pass 처리 |

### 1-5. PM/기획에게 지금 건네는 묶음
| 단계 | 먼저 전달할 문서 | 이번 턴 기록 | 금지 문장 |
| --- | --- | --- | --- |
| Commit 1 | `runtime commit stack`, `runtime handoff scorecard`, `runtime owner workboard` | `이번 턴은 overrunEvent floor green이다.` | `오버런 거의 완료`, `runtime-ready` |
| Commit 2 | 위 문서 + `second runtime review` | `이번 턴은 overrunEvent runtime-ready closing green이다.` | `오버런/온보딩 같이 green` |
| B-1 | `onboarding runtime stack`, `handoff scorecard`, `owner workboard` | `이번 턴은 library primer green이다.` | `온보딩 green` |
| B-2 | 위 문서 + `B-2 packet` | `이번 턴은 battle primer green이다.` | `library+battle green` |
| B-3 | 위 문서 + `B-3 packet` | `이번 턴은 reward/shop primer green이다.` | `reward/shop/ending green` |
| B-4 | 위 문서 + `B-4 packet` + `boundary packet` | `이번 턴은 ending primer green이다.` | `ending + overrun flavor green` |

## 2. 단계별 handoff 표
| 단계 | 누구에게 실제로 넘겨야 하나 | 지금 턴의 필수 산출물 | 다음 턴으로 넘겨야 하는 것 |
| --- | --- | --- | --- |
| Commit 1 | 프로그래머, QA, PM | enter/snapshot floor + recovery + non-change evidence | UI/아트 closing은 Commit 2로 이월 |
| Commit 2 | 프로그래머, UI, 아트, QA, PM | scene/resolve/runtime-ready closing | onboarding B-track 전체 |
| B-1 | 프로그래머, UI, QA, PM | library primer green | battle/reward/shop/ending |
| B-2 | 프로그래머, UI, QA, PM | battle primer green | reward/shop, ending |
| B-3 | 프로그래머, UI, 아트, QA, PM | reward/shop primer green | ending |
| B-4 | 프로그래머, UI, 아트, QA, PM | ending primer green + boundary 유지 | onboarding polish 이상의 후속 확장 |

## 3. UI/아트 마감 타이밍
### 지금 꼭 기억해야 할 경계
- **Commit 1**: UI/아트는 `마감`이 아니라 `검수/메모` 단계다.
- **Commit 2**: `overrunEvent` 장면 카드의 실제 readable closing이 열리는 첫 턴이다.
- **B-1/B-2**: 온보딩은 주입 위치와 밀도 sanity가 우선이다.
- **B-3/B-4**: reward/shop 목적 분리, ending의 절제된 helper가 실제 마감 포인트다.

### 왜 이게 중요한가
이 타이밍이 흐려지면 세 가지가 바로 무너진다.
1. Commit 1에서 half-cutover가 완성처럼 보인다.
2. B-3 이전에 reward/shop/ending이 같은 primer 톤으로 수렴한다.
3. B-4에서 ending이 `overrunEvent` flavor를 먹어 boundary가 사라진다.

## 4. 제출 증거 규칙
| 단계 | 최소 증거 | 있으면 좋은 보조 증거 | tracker/run log 문장 |
| --- | --- | --- | --- |
| Commit 1 | smoke, 상태 diff, non-change line, 코드 diff | snapshot field set 캡처 | `이번 턴은 overrunEvent floor green이다.` |
| Commit 2 | smoke migration, UI/DOM evidence, resolve 1회 적용, 코드 diff | `resolved` reload guard 캡처 | `이번 턴은 overrunEvent runtime-ready closing green이다.` |
| B-1 | 첫 노출/재노출/비차단 evidence | seen 필드 persistence 캡처 | `이번 턴은 library primer green이다.` |
| B-2 | entry/first-spell/auto-pass trigger evidence | battle 재진입 캡처 | `이번 턴은 battle primer green이다.` |
| B-3 | reward/shop 목적 분리 evidence | boss draft 공존 캡처 | `이번 턴은 reward/shop primer green이다.` |
| B-4 | ending primer + boundary 유지 evidence | overrun CTA 공존 캡처 | `이번 턴은 ending primer green이다.` |

## 5. 리스크 & 후속과제
- 가장 큰 리스크는 여전히 문서 부족이 아니라 **오너 handoff가 다시 큰 묶음 문장으로 돌아가는 것**이다.
- Commit 1에서 UI/아트 산출물을 `마감`처럼 취급하면 Commit 2 closing의 의미가 사라진다.
- B-track에서 PM이 `온보딩 거의 완료` 같은 문장을 쓰면 B-1~B-4의 owner handoff 분리가 무너진다.
- B-4에서 ending helper가 overrun flavor를 흡수하면 A-track에서 어렵게 지킨 경계가 가장 먼저 깨진다.
- 후속 문서 작업은 새 spec 추가보다, 이 패킷이 가리키는 오너별 전달 묶음이 tracker/index/master plan/workboard와 계속 같은 언어를 쓰는지 유지하는 쪽이 맞다.

## 즉시 다음 액션
1. 다음 writer 구현 슬롯은 여전히 A-track Commit 1부터 닫는다.
2. Commit 1에서는 프로그래머/QA/PM handoff까지만 green으로 남긴다.
3. Commit 2 closing review green 뒤에만 B-track을 연다.
4. B-track은 반드시 `runtime stack -> handoff scorecard -> owner workboard -> 해당 B packet` 순서로 읽고, `surface 1개 = owner handoff 1개` 원칙으로 닫는다.
5. tracker/run log에는 각 단계의 green 문장을 그대로 복붙하고, 합쳐 쓰지 않는다.
