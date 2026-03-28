# DiceSpell `Run Table` Battle Filled Archive Examples 요약

## 3줄 요약
- 이 문서는 `battle-stakes-plaque` 첫 stage의 artifact 8종이 **실제로 어느 문장 밀도까지 채워져야 honest archive인지** 빠르게 읽게 만드는 companion view다.
- 핵심은 새 전투 UI 설계가 아니라, `manifest / hook / check / capture caption / density / starter / handoff`가 모두 같은 battle stage 언어를 쓰게 만드는 것이다.
- 첫 화면만 읽어도 `candidate는 언제 쓰는지`, `green은 누가 마지막으로 쓰는지`, `oath unlock은 왜 아직 보류인지`가 바로 보여야 한다.

## 한 줄 목표
battle archive를 `파일만 있는 폴더`가 아니라 **같은 stage 문장을 공유하는 filled bundle**로 보이게 만든다.

## 왜 이 문서를 읽어야 하나
- 프로그래머: manifest와 hook/check note를 어느 밀도까지 채우면 UI/아트 evidence를 받을 수 있는지 확인한다.
- UI: overview/stakes-focus capture도 battle-only 질문과 짧은 caption sentence가 필요하다는 점을 확인한다.
- 아트: starter note는 큰 combat shell 발주 문서가 아니라 이번 stage를 닫는 작은 sanity note라는 점을 확인한다.
- QA/PM: candidate와 green을 exemplar 문장 그대로 분리해 기록해야 한다는 점을 확인한다.

## core structure

| artifact 축 | exemplar가 보여 주는 것 | 아직 쓰면 안 되는 것 |
| --- | --- | --- |
| manifest | stage status / boundary / next allowed step | `Battle Table 진행`, `oath도 가능` |
| hook + dom sanity | battle root/slot/hierarchy 책임과 존재 확인 | action panel/log/oath 확장 계획 |
| capture caption 2종 | overview / stakes-focus 질문과 pass/fail 문장 | glamour shot, combat HUD 총평 |
| density review | `유지 / 약함 / 과함` verdict와 battle-only fix point | 전체 전투 UI 회고 |
| starter asset list | Stakes Plaque Starter 충분성 + 비범위 | combat shell 리뉴얼, oath starter 확장 |
| handoff line | candidate / green 공식 복붙 문장 | 큰 총괄 green 문장 |

## 역할별 handoff 포인트
- **프로그래머**: `manifest -> hook-manifest -> dom-sanity`를 먼저 채운다.
- **UI**: `overview/stakes-focus png + caption sentence + density verdict`를 battle-only로 맞춘다.
- **아트**: `starter-asset-list`에 충분성/비범위를 같이 적는다.
- **QA**: exemplar 수준의 wording alignment가 맞을 때만 `archive-ready candidate`를 쓴다.
- **PM**: 최종 문장은 `battle-stakes-plaque green, oath-banner unlock: battle second-stage evidence bundle과 handoff line 정렬 확인.`만 쓴다.

## 리스크 & 후속과제
- exemplar 없이 archive를 열면 note가 다시 generic progress memo로 흐를 수 있다.
- capture caption이 없으면 1440p evidence가 감상형 스크린샷으로 남을 수 있다.
- starter note가 커지면 battle stage가 combat shell 전체나 oath까지 다시 부풀 수 있다.
- candidate와 green이 run log에서 다시 같은 초록불로 기록될 수 있다.

## immediate next actions
1. `battle filled archive examples`를 먼저 읽고 live archive에 같은 구조로 옮긴다.
2. `.png` capture는 exemplar caption sentence와 같이 저장한다.
3. QA는 candidate만, PM은 green만 기록한다.
4. action panel, dice tray, log, oath-banner는 battle green 전까지 계속 잠근다.

## 한 장 결론
Run Table battle 축의 남은 마지막 공백은 `무슨 artifact가 필요한가`가 아니라 **`그 artifact를 실제로 어떤 문장으로 채워야 honest archive가 되는가`**다. 이 요약 문서는 그 답을 빠르게 보여 주는 readable companion이다.
