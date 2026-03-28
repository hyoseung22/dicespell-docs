# DiceSpell Standalone 소스코드 구조 정리

## 0. 문서 목적

이 문서는 Claude나 다른 개발자가 현재 프로젝트를 빠르게 이해하고, 어느 파일이 무엇을 담당하는지 혼동 없이 따라갈 수 있도록 만든 코드 구조 요약서다.

역기획서가 “무엇을 구현해야 하는가”에 집중한다면, 이 문서는 “현재 저장소에서 어디를 읽어야 하는가”와 “무엇이 실제 실행 경로인가”에 집중한다.

> 주의: 이 문서는 **현재 코드베이스의 실행 경로**를 설명하는 baseline 문서다. `overrunEvent` 컷오버 목표 계약은 여기서 완결되지 않으며, 해당 범위는 `docs/dicespell_overrun_event_source_of_truth_map_kr.md`와 `docs/dicespell_overrun_event_*` 문서 묶음을 우선한다. 즉 여기서 `continueOverrun()`가 즉시 적용 함수처럼 보이는 것은 문서 오류가 아니라 아직 코드가 그 상태라는 뜻이다.

## 1. 저장소 구조

루트 기준 핵심 경로:

- `package.json`
- `dice-spell-standalone/main.mjs`
- `dice-spell-standalone/preload.mjs`
- `dice-spell-standalone/src/index.html`
- `dice-spell-standalone/src/app.js`
- `dice-spell-standalone/src/game-engine.js`
- `dice-spell-standalone/src/data.js`
- `dice-spell-standalone/src/audio.js`
- `dice-spell-standalone/src/styles.css`
- `dice-spell-standalone/scripts/smoke-test.mjs`
- `dice-spell-standalone/scripts/balance-sim.mjs`
- `docs/dicespell_current_build_reverse_spec_kr.md`

### 1.1 루트 `package.json`

역할:

- Electron 앱 진입 스크립트 정의
- 스모크 테스트 정의
- 밸런스 시뮬레이터 CLI 정의
- 패키징 및 설치 빌드 설정

핵심 스크립트:

- `npm start`
- `npm run smoke`
- `npm run balance:sim`
- `npm run portable:win`
- `npm run installer:win`

## 2. Electron 계층

### 2.1 `main.mjs`

역할:

- BrowserWindow 생성
- 창 최소 크기/기본 크기 관리
- 해상도 프리셋 IPC 처리
- 밸런스 시뮬레이터 IPC 처리

현재 주요 동작:

- 기본 창 크기: `1600 x 900`
- 최소 창 크기: `1280 x 720`
- IPC:
  - `window:set-resolution-preset`
  - `balance:run-report`

중요 포인트:

- 실제 밸런스 시뮬레이터는 렌더러에서 직접 계산하지 않고 메인 프로세스에서 `runBalanceSimulation()`을 호출한다.
- 해상도 프리셋은 활성 디스플레이 작업 영역에 맞춰 clamp 된다.

### 2.2 `preload.mjs`

역할:

- 렌더러에서 Electron IPC를 직접 만지지 않도록 안전 브리지 제공

노출 API:

- `window.dicespellDesktop.setResolutionPreset(presetId)`
- `window.dicespellDesktop.runBalanceSimulation(options)`

## 3. 프런트엔드 엔트리

### 3.1 `src/index.html`

역할:

- 최소한의 HTML 쉘
- 실제 화면은 대부분 `app.js`가 렌더한다.

### 3.2 `src/styles.css`

역할:

- 전역 테마
- 로비 / 전투 / 상점 / 보상 / 엔딩 레이아웃
- 반응형 및 해상도 대응
- 드롭다운, 로그, 카드 스타일

주의점:

- 해상도 드롭다운은 포털 스타일 오버레이를 사용한다.
- 전투 화면은 1440x900 전후에서 내부 스크롤이 과도하게 생기지 않도록 밀도가 조정되어 있다.

## 4. 정적 데이터 파일

### 4.1 `src/data.js`

역할:

- 저장 키 상수
- 속성 메타
- 책 데이터
- 족보 데이터
- 몬스터 템플릿
- 아티팩트 데이터
- 붕괴 단계 데이터
- 희귀도 색상 메타

이 파일은 “밸런스 테이블과 콘텐츠 데이터의 소스 오브 트루스”다.

### 4.2 현재 포함된 데이터 규모

- 저장 키: 1개
- 속성: 5종
- 책: 18권
- 족보: 13종
- 몬스터 테마: 3세트
- 아티팩트: 61종
- 붕괴 단계: 6단계

### 4.3 데이터 파일을 읽을 때 주의점

- 터미널에서 직접 `Get-Content`로 보면 일부 한국어 문자열이 깨질 수 있다.
- 그러나 `import`해서 런타임 값으로 읽으면 정상 값이 나오는 항목이 많다.
- 텍스트 자체가 혼란스러우면 역기획서의 부록 테이블을 기준값으로 삼는 편이 안전하다.

## 5. 게임 엔진

### 5.1 `src/game-engine.js`의 역할

이 파일은 프로젝트의 핵심이다.

- 런 생성
- 상태 정규화
- 전투 판정
- 피해 계산
- 몬스터 생성과 행동
- 보상 생성과 적용
- 상점 생성과 구매 처리
- 상단 합계 보너스
- 엔딩 판정
- 요약 뷰모델 생성

실질적으로 게임 규칙 대부분은 이 파일 하나에 모여 있다.

### 5.2 외부로 노출되는 주요 API

현재 export 기준 주요 함수:

- `DEFAULT_BALANCE_SETTINGS`
- `normalizeBalanceSettings`
- `createNewRun`
- `createInitialViewModel`
- `resumeState`
- `getSessionSummary`
- `rollDice`
- `toggleLock`
- `selectOrCastHand`
- `skipToMonsterTurn`
- `chooseUpperBonus`
- `buyShopOffer`
- `buyArtifact`
- `chooseReward`
- `infuseHand`
- `chooseBossArtifact`
- `continueAfterReward`
- `leaveShop`
- `getArtifactObjects`
- `getCollapseTier`
- `rarityMeta`

### 5.3 엔진 내부를 이해하는 추천 읽기 순서

Claude나 신규 개발자에게 추천하는 읽기 순서:

1. 상단 상수와 기본값
2. `normalizeBalanceSettings`
3. `createHandState`
4. `normalizeState`
5. `createPagePlan`
6. `createMonster`, `spawnStageMonsters`
7. `evaluateHand`
8. `artifactTotals`
9. `calculateFinalDamage`
10. `startTurn`, `rollDice`, `selectOrCastHand`, `runMonsterTurn`
11. `createRewardPhase`, `createShopDraft`
12. `createNewRun`
13. `getSessionSummary`

### 5.4 엔진 내 핵심 하위 시스템

#### 상태 정규화

`normalizeState(state)`

역할:

- 저장 데이터 호환성 보정
- 손상된 필드 기본값 보완
- `handSlots` 구조 보장
- `battle.reward.shop` 배열 정상화
- 몬스터 HP/intent 보정

중요 포인트:

- 구버전 저장 데이터에서 스폰 몬스터의 `maxHp`만 큰 값으로 남는 문제를 여기서 보정한다.

#### 족보 판정

`evaluateHand(handId, dice)`

역할:

- 현재 주사위로 특정 족보가 맞는지
- 기본 피해량이 얼마인지
- 상단 점수는 얼마인지

#### 아티팩트 집계

`artifactTotals(state, context)`

역할:

- 플레이어가 가진 모든 아티팩트를 현재 문맥에 맞게 집계
- 피해 배율, 비용 감소, 상태 보너스, 상점 할인 등을 하나의 totals 객체로 반환

이 함수가 사실상 빌드 시스템의 핵심 허브다.

#### 피해 계산

`calculateFinalDamage(state, hand, monster, matchInfo)`

역할:

- 족보 기본 피해 + 강화석 보너스
- 아티팩트 배율
- 붕괴 배율
- 속성 상성
- 상태이상 피격 배율

#### 상태이상 처리

주요 함수:

- `applyStatus`
- `processTurnStartStatuses`
- `advanceStatusesAfterTurn`
- `applyElementStatusToTarget`
- `applyMonsterElementStatusToPlayer`

#### 전투 액션

주요 함수:

- `startTurn`
- `rollDice`
- `toggleLock`
- `selectOrCastHand`
- `skipToMonsterTurn`
- `runMonsterTurn`
- `monsterAct`
- `finalizeBattleAction`

#### 보상과 상점

주요 함수:

- `createElementStoneReward`
- `createUpgradeStoneReward`
- `createHealingReward`
- `createRewardPhase`
- `createShopDraft`
- `buyShopOffer`
- `chooseReward`
- `chooseBossArtifact`
- `continueAfterReward`
- `leaveShop`

## 6. UI 렌더러

### 6.1 `src/app.js`의 역할

`app.js`는 상태를 들고 전체 화면을 문자열 기반으로 렌더한다.

구조적으로는:

- 로컬 상태 관리
- 저장 불러오기 / 저장하기
- 렌더 함수 집합
- 이벤트 위임
- 오디오 동기화
- Electron 브리지 호출

React나 Vue 같은 프레임워크를 쓰지 않는다. 완전히 수제 렌더링 구조다.

### 6.2 앱 상태(`appState`)

`appState`는 세션 외에도 UI 상태를 가진다.

주요 필드:

- `session`
- `pendingTargetHandId`
- `lobbySettings`
- `displaySettings`
- `libraryView`
- `balanceArtifactPage`
- `grimoirePage`
- `playerPanelTab`
- `playerArtifactPage`
- `displayMenuOpen`
- `displayMenuPosition`
- `balanceReport`

### 6.3 `app.js`에서 특히 중요한 함수들

#### 세이브/복구

- `readSavedSession`
- `clearSavedRun`
- `persistLobbySettings`
- `readLobbySettings`
- `readDisplaySettings`

#### 해상도/메뉴

- `setDisplayPreset`
- `updateDisplayMenuPosition`
- `closeDisplayMenu`
- `renderDisplayMenuPortal`

#### 로비 렌더

- `renderLibraryNav`
- `renderGuidePage`
- `renderBalanceLab`
- `renderGrimoireShelf`
- `renderBalanceReportPage`
- `renderLibrary`

#### 전투/런타임 렌더

- `renderPlayerStatusView`
- `renderPlayerArtifactView`
- `renderPlayerPanel`
- `renderRightPanel`
- `renderUpperSectionPanel`
- `renderUpperBonusDraft`
- `renderBattle`
- `renderShop`
- `renderReward`
- `renderEnding`
- `renderCenter`

#### 루트 렌더

- `render(hasRecovered, options)`

### 6.4 렌더링 방식

특징:

- 거의 모든 UI가 템플릿 문자열 기반으로 만들어진다.
- 화면이 바뀔 때마다 루트 내부를 다시 렌더한다.
- 일부 상태는 렌더 전후에 보존한다.
  - 도서관 스크롤 위치
  - 타겟 선택 상태 일부
  - 패널 탭 상태

### 6.5 이벤트 처리 방식

이 파일은 중앙 이벤트 위임 구조를 사용한다.

버튼은 `data-action` 속성을 달고, 이벤트 핸들러가 이를 읽어 분기한다.

예:

- `choose-grimoire`
- `resume-run`
- `run-balance-report`
- `roll-dice`
- `toggle-lock`
- `select-hand`
- `choose-reward`
- `buy-shop-offer`
- `choose-boss-artifact`
- `continue-reward`
- `leave-shop`

### 6.6 뷰모델 사용 방식

UI는 직접 세션 상태를 다 파고들지 않고, 엔진의 `getSessionSummary(state)` 결과를 많이 사용한다.

이 요약 객체 안에는 다음이 들어 있다.

- 현재 책 정보
- 붕괴 단계
- 아티팩트 실체 목록
- 플레이어 상태이상 요약
- 족보 카드용 요약 정보
- 몬스터 패널용 요약 정보
- 상단 합계 정보
- 현재 페이지 정보

즉, “화면 표시용 파생 데이터”는 엔진에서 계산하고, 렌더러는 이를 그린다.

## 7. 오디오 계층

### 7.1 `src/audio.js`

역할:

- BGM/SFX 경로 관리
- localStorage 기반 오디오 설정 유지
- 배경음 전환
- 효과음 재생

주요 개념:

- `AudioManager` 클래스를 사용한다.
- `audioManager` 싱글턴을 export 한다.

### 7.2 핵심 메서드

- `getPrefs`
- `toggleBgm`
- `toggleSfx`
- `playSfx`
- `playHover`
- `playMusic`
- `stopMusic`
- `syncMusic`

### 7.3 배경음 선택 규칙

- `library` phase -> library BGM
- `battle`
  - 보스/최종 보스 -> boss BGM
  - 일반 -> normal battle BGM
- `shop` -> shop BGM
- `reward`
  - 직전 battle BGM 유지
- `ending`
  - victory -> clear
  - fail -> fail

## 8. 밸런스 시뮬레이터

### 8.1 `scripts/balance-sim.mjs`

역할:

- CLI용 밸런스 실험
- GUI용 승률 리포트 계산

### 8.2 구현 원칙

이 스크립트는 가짜 수학 모델이 아니라 실제 게임 엔진 함수를 재사용한다.

즉:

- `createNewRun`
- `rollDice`
- `selectOrCastHand`
- `chooseReward`
- `buyShopOffer`
- `chooseBossArtifact`
- `continueAfterReward`

등을 실제로 호출하며 자동 플레이한다.

### 8.3 주요 구성요소

- 인자 파서
- build profile 정의
- skill profile 정의
- 주문/잠금/상점/보상 선택 휴리스틱
- 전투 루프
- 통계 집계
- 페이지별 도달률/클리어율 리포트
- 다중 마도서 비교용 `grimoireIds` 집계와 책별 평균/사용군 평균/숙련도 평균 리포트
- 체크포인트 진단용 `checkpoints` 입력과 `Checkpoint Diagnostic Report` 출력
- `토석 압인본(1200008)` 전용 `Ground Collapse Timing Report` 출력 (`AvgRelay<=P`, `AvgWard<=P`, `BossRelayShare` 포함)
- 시뮬레이션 전용 `starterArtifacts` / `pageCountOverride` override와 100회 이상 공통 배치 비교 운용

### 8.4 CLI 진입

`npm run balance:sim -- --runs=24 --hp=43`

또는 공통 배치 비교용으로:

`npm run balance:sim -- --grimoires=1200005,1200006,1200007,1200008 --runs=100 --hp=75,85,100`

가설 분리가 필요하면 다음처럼 시뮬레이션 전용 override도 줄 수 있다.

`npm run balance:sim -- --grimoire=1200009 --runs=24 --hp=75,85,100 --starter-artifacts=70058,70020 --label=starter-mana-on-kill`

`npm run balance:sim -- --grimoire=1200009 --runs=24 --hp=75,85,100 --page-count=15 --label=page15-baseline-starters`

같은 방식으로 호출한다.

체크포인트 진단이 필요하면 다음처럼 호출한다.

`npm run balance:sim -- --grimoires=1200007,1200008 --runs=100 --hp=100 --checkpoints=5,10,15`

`1200008`의 `깊은 붕락` 발동 타이밍과 `붕락 잔진` 환전량, `균열 차폐` 완충량(`AvgRelay<=P`, `AvgWard<=P`, `BossRelayShare`)까지 같이 보고 싶다면 같은 호출만으로 `Ground Collapse Timing Report`가 함께 출력된다.

최근 `전격 실험지(1200007)` 단일보스 보강 검증에는 다음 호출을 사용했다.

`npm run balance:sim -- --grimoire=1200007 --runs=100 --hp=100 --checkpoints=5,10,15`

이 호출에서는 HP 100 기준 승률, 페이지 5/10/15 체크포인트 실패율, 평균 HP/붕괴/유물/주입 수를 함께 확인해 `전하 환류 + 보스 과전류`가 실제로 후반 단일보스 마무리에 기여하는지 읽는다.

### 8.5 GUI 진입

로비의 `4. 승률 리포트` 탭에서 IPC를 통해 실행한다.

## 9. 테스트

### 9.1 `scripts/smoke-test.mjs`

역할:

- 핵심 플레이 흐름이 망가지지 않았는지 빠르게 점검

대표적으로 확인하는 것:

- 런 생성
- 전투/보상/상점 흐름
- 밸런스 세팅 반영
- 상단 합계 보너스
- 최근 추가된 보상/상점 시스템
- `1200006` 빙결 재타격 추가 냉기 4 피해 회귀
- 로그/상태 복구 관련 회귀

### 9.2 테스트 철학

현재 저장소는 단위 테스트 프레임워크보다 “엔진 스모크 테스트 + 직접 실행 확인”에 가깝다.

## 10. 현재 코드베이스의 비정상 요소

Claude가 코드를 읽을 때 가장 헷갈릴 수 있는 부분이다.

### 10.1 죽은 코드

몇몇 함수는 상단에 현재 구현이 있고, 그 아래에 이전 구현이 `return` 뒤에 남아 있다.

대표 함수:

- `createRewardPhase`
- `createShopDraft`
- `renderShop`
- `renderReward`
- `buyArtifact`

의미:

- 파일 안에 코드가 두 벌처럼 보일 수 있다.
- 그러나 실제로는 상단의 새 구현만 사용된다.

### 10.2 로그 문자열 정규화

`addLog()`는 일부 깨진 문자열 조각을 런타임에 정상 문장으로 치환한다.

즉:

- 로그 텍스트가 소스코드에 완전히 깨져 보여도
- 실행 결과는 비교적 정상일 수 있다.

Claude가 로그 문구를 복원해야 한다면, raw source보다 UI 출력과 역기획서를 기준으로 문장을 다시 쓰는 편이 낫다.

### 10.3 `buyArtifact`, `infuseHand`의 현재 의미

- `buyArtifact()`는 사실상 `buyShopOffer()`에 위임만 하는 호환 함수다.
- `infuseHand()`는 예전 보상 구조 흔적이 남아 있지만, 현재 핵심 보상 루프는 즉시 적용형 정령석/강화석 구조다.

## 11. Claude에게 권장하는 재구현 순서

1. `data.js`를 먼저 재구성한다.
2. `game-engine.js`의 상태 스키마와 플로우를 뼈대로 만든다.
3. 전투 루프만 먼저 구현한다.
4. 보상과 상점을 붙인다.
5. 저장/복구를 붙인다.
6. 로비 밸런스 세팅을 붙인다.
7. 오디오와 해상도 옵션을 붙인다.
8. 마지막으로 승률 시뮬레이터를 붙인다.

이 순서가 좋은 이유:

- 현재 프로젝트는 엔진 중심 구조라서, UI보다 상태 전이가 먼저 맞아야 한다.
- UI는 요약 뷰모델을 읽는 구조이므로, 엔진이 맞으면 화면은 따라가기가 쉽다.

## 12. 재구현 시 “무조건 맞아야 하는” 파일 단위 기준

### 12.1 엔진 기준

- 책 선택 후 바로 첫 페이지 상태가 생성되어야 한다.
- 페이지 플랜 규칙이 동일해야 한다.
- 전투 첫 턴 MP/리롤 규칙이 동일해야 한다.
- 붕괴 단계표와 발동 확률이 동일해야 한다.
- 상단 합계 보너스 드래프트가 존재해야 한다.
- 보상 및 상점 스톤 구조가 동일해야 한다.

### 12.2 UI 기준

- 로비 4탭 구조가 있어야 한다.
- 좌측 상태/아티팩트 탭 전환 패널이 있어야 한다.
- 우측 최근 로그와 페이지 진행 맵이 있어야 한다.
- 해상도 드롭다운이 헤더 오버레이 방식이어야 한다.

### 12.3 저장 기준

- localStorage 키 이름이 동일해야 한다.
- 로비 설정과 디스플레이 설정이 런 세이브와 분리되어야 한다.

## 13. 요약

이 프로젝트는 겉보기보다 구조가 단순하다.

- 데이터는 `data.js`
- 규칙은 `game-engine.js`
- 화면은 `app.js`
- 사운드는 `audio.js`
- 데스크톱 브리지는 `main.mjs`, `preload.mjs`
- 시뮬레이터는 `balance-sim.mjs`

즉, Claude가 현재 빌드를 이해하려면 먼저 `game-engine.js`를 중심축으로 잡고, 그 주변에 데이터와 UI를 감싸는 방식으로 읽으면 된다.
