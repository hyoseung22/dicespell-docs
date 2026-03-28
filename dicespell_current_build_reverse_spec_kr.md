# DiceSpell Standalone 현재 빌드 역기획서

## 0. 문서 목적

이 문서는 `C:\Users\zas87\OneDrive\문서\dicespell-standalone` 워크스페이스의 현재 구현 상태를 기준으로, 다른 LLM이나 개발자가 최대한 동일한 게임을 재구현할 수 있도록 정리한 역기획서다.

핵심 목표는 다음과 같다.

- 현재 Electron 빌드의 플레이 흐름, 데이터 구조, UI 구성, 수치 시스템, 저장 구조를 빠짐없이 설명한다.
- 구현자가 원본 소스코드를 직접 읽지 않아도 전체 시스템을 복원할 수 있도록 한다.
- 원본 코드에 일부 죽은 코드(dead code)와 문자열 인코딩 문제가 남아 있으므로, 이 문서를 사람이 읽는 기준 사양서로 사용한다.
- 수치와 확률은 설명형이 아니라 실제 코드 기준값으로 기록한다.

기준 시점은 `2026-03-15`이며, 현재 저장소의 스탠드얼론 데스크톱 빌드를 기준으로 한다.

> 주의: 이 문서는 **현재 구현 상태**를 복원하기 위한 역기획서다. 따라서 오버런 보관함 전리품의 `즉시 적용` 서술은 현재 코드 기준으로는 맞지만, 다음 컷오버 목표는 아니다. `overrunEvent` phase, confirm 시점 적용, recovery/fallback 계약은 `docs/dicespell_overrun_event_source_of_truth_map_kr.md`와 `docs/dicespell_overrun_event_*` 문서 묶음을 우선 source of truth로 읽는다.

## 1. 제품 정의

### 1.1 한 줄 정의

`DiceSpell Standalone`은 야추/주사위 족보 판정, 로그라이크 성장, 상태이상 기반 속성 시너지, 붕괴 리스크 관리, 책 선택형 스타팅 변주, 런 종료 후 메타 해금을 결합한 데스크톱 프로토타입이다.

### 1.2 플레이 감각

- 플레이어는 로비에서 마도서 한 권을 고른다.
- 각 페이지는 `전투`, `상점`, `보상`, `보스`, `최종 보스`로 이어지는 선형 진행 구조다.
- 책을 끝까지 봉인하면 즉시 `오버런`으로 다음 책 구간을 이어서 플레이할 수 있다.
- 도중 실패하거나 직접 도서관으로 돌아가도 완료한 페이지 수만큼 `서고 기록`이 남아 다음 해금에 쓰인다.
- 전투는 `Roll -> Lock -> Spell(턴당 1회) -> Monster Turn` 구조로 진행된다.
- 족보를 사용할수록 슬롯이 잠기고, 붕괴가 누적되며, 장기전 압박이 생긴다.
- 속성 주입과 아티팩트가 빌드의 방향을 크게 바꾼다.
- 현재 빌드는 완성형 상용 게임이 아니라, 시스템 검증과 밸런스 실험을 위한 스탠드얼론 프로토타입이다.

### 1.3 플랫폼과 실행 방식

- 런타임: Electron
- 렌더러: 순수 HTML/CSS/JavaScript
- 저장: `localStorage`
- 패키징: `electron-builder`, `electron-packager`
- 기본 실행: `npm start`

## 2. 전체 화면 구조

### 2.1 상단 공통 헤더

모든 주요 화면에서 상단 헤더가 유지된다.

- 좌측: 게임 타이틀과 현재 문맥 표시
- 우측:
  - `BGM On/Off`
  - `SFX On/Off`
  - 해상도 드롭다운
  - 세이브
  - 도서관 복귀

### 2.2 도서관(로비) 화면

도서관은 현재 4개의 탭으로 구성된다.

1. `게임 안내`
2. `밸런스 세팅`
3. `도서관 책 선택`
4. `승률 리포트`

### 2.3 전투 화면

전투 화면은 3열 구조다.

- 좌측 패널:
  - 마도사 상태 탭
  - 보유 아티팩트 탭
- 중앙 메인:
  - 전투 헤더
  - 주사위 테이블
  - 몬스터 시야
  - 기억 회람판(13개 족보 카드)
  - 상단 합계 보너스 패널
- 우측 패널:
  - 페이지 진행 맵
  - 최근 로그

### 2.4 상점 화면

- 상점 카드 그리드로 표시된다.
- 현재 4개 슬롯 구조다.
  - 아티팩트 2칸
  - 정령석 1칸
  - 강화석 1칸
- 구매 즉시 효과가 적용된다.
- 전부 보고 난 뒤 `다음 페이지로` 버튼으로 진행한다.

### 2.5 보상 화면

- 일반 스테이지 클리어 시:
  - 일반 보상 3개 중 1개 선택
- 보스/최종 보스 클리어 시:
  - 일반 보상 3개 중 1개 선택
  - 추가로 아티팩트 드래프트 3개 중 1개 선택

### 2.6 엔딩 화면

- 승리 또는 패배 이유를 표시한다.
- 런 정산 카드에서 이번 런이 남긴 `서고 기록`, 새로 열린 마도서/유산 해금을 표시한다.
- 책을 끝까지 클리어한 경우 `오버런 계속` 버튼이 열려 다음 책 구간으로 이어진다.
- 승리 엔딩에서는 추가로 `오버런 전리품 보관함` 3택 1이 나타날 수 있다. 현재 책 테마 전리품과 이번 세그먼트 동안 실제로 처치해 기록한 특수 적 전리품 중 최대 3개를 다시 보여 주고, 그중 1개를 고르면 다음 오버런 진입 직전에 즉시 적용한다.
- 도서관으로 돌아가서 새 런을 시작할 수 있다.

## 3. 런 구조

### 3.1 페이지 플랜 규칙

페이지 플랜은 책마다 총 페이지 수가 다르지만 규칙은 공통이다.

- 마지막 페이지: `FinalBoss`
- 마지막 페이지가 아닌 5의 배수: `Boss`
- 상점 페이지: `3, 8, 13, 18, 23, 28`
- 그 외: `Normal`

예시:

- 15페이지 책
  - Shop: 3 / 8 / 13
  - Boss: 5 / 10
  - FinalBoss: 15
- 30페이지 책
  - Shop: 3 / 8 / 13 / 18 / 23 / 28
  - Boss: 5 / 10 / 15 / 20 / 25
  - FinalBoss: 30

### 3.2 페이지 타입별 처리

- `Normal`
  - 전투 후 일반 보상 1회
- `Boss`
  - 전투 후 일반 보상 1회 + 보스 아티팩트 드래프트 1회
- `FinalBoss`
  - 전투 후 승리 엔딩
- `Shop`
  - 상점 구매 후 다음 페이지 이동

### 3.3 적 배치 수

일반 스테이지의 적 수는 페이지에 따라 증가한다.

- 페이지 1~5: 1마리
- 페이지 6~15: 2마리
- 페이지 16 이상: 3마리

보스와 최종 보스는 항상 1마리다.

적 종류 선정은 고정 테마 순환이 아니라 `난이도 기반 랜덤 스폰풀`로 처리된다.

- 노멀 전투:
  - 전체 노멀 몬스터 풀에서 현재 `segmentPage`와 `overrunLevel`에 맞는 목표 티어를 만든다.
  - 목표 티어와 가까운 후보군만 추린 뒤 가중 랜덤으로 뽑는다.
- 보스/최종 보스:
  - 각각 별도의 보스 풀, 최종 보스 풀에서 같은 방식으로 뽑는다.
- 책 테마 가중치:
  - 현재 책의 기본 테마와 같은 몬스터는 더 자주 나오도록 가중치 보너스를 받는다.
  - 단, 다른 테마 몬스터도 섞일 수 있어 반복감을 줄인다.
- 편성 역할 슬롯:
  - 2마리 전투는 `전열/방벽`과 `술사` 축을 우선 채우도록 가중치를 준다.
  - 3마리 전투는 세 번째 슬롯에서 `교란` 역할을 우선 찾는다.
- 중복 완화:
  - 이미 전장에 있는 템플릿은 가중치를 낮춰 같은 적만 복제되는 상황을 줄인다.
  - 이미 전장에 있는 편성 역할도 가중치를 낮춰 역할이 겹치지 않게 조정한다.

### 3.4 오버런 구조

- 최종 보스 격파 시 런은 즉시 끝나지 않고 `ending.canOverrun = true` 상태가 된다.
- 이 시점에 현재 책의 `완주 보너스`가 즉시 적용되어 다음 세그먼트 준비가 끝난 상태로 엔딩 화면에 들어간다.
- 이번 세그먼트 동안 `기록 골렘`/`붕락 정령`처럼 전리품 정의가 있는 특수 적을 실제로 처치했다면, 그 `templateId`를 런 상태의 `segmentTrophyTemplateIds`에 누적한다.
- 승리 엔딩에서는 현재 책 테마 전리품 + `segmentTrophyTemplateIds` 기반 적 전리품 중 최대 3개를 `ending.overrunDraft.options`로 보여 주고, 선택한 1개를 다음 오버런 진입 직전에 즉시 적용한다. 선택하지 않고 바로 넘기는 것도 허용된다.
- 플레이어가 오버런을 선택하면 같은 마도서의 페이지 플랜이 한 세그먼트 더 뒤에 붙고, 선택한 보관함 전리품이 먼저 적용된 뒤 다음 전투 페이지로 진입한다.
- 절대 페이지 번호(`page`)는 계속 증가하지만, 보스/상점 배치는 세그먼트 페이지(`segmentPage`) 기준으로 다시 계산한다.
- 오버런 레벨이 올라갈수록 몬스터 HP/공격 배율이 누적 상승한다.
- 현재 배율은 기본적으로 오버런 1레벨마다 `x1.08`이 곱해지고, 3레벨마다 추가 `x1.16`이 더해진다.

## 4. 저장 키와 영속 데이터

`localStorage`를 사용한다.

- 런 세이브: `dicespell-standalone-save`
- 메타 진행도: `dicespell-standalone-meta`
- 로비 밸런스 세팅: `dicespell-standalone-lobby-settings`
- 디스플레이 설정: `dicespell-standalone-display-settings`
- 오디오 설정: `dicespell-standalone-audio-prefs`

현재는 멀티 슬롯 세이브가 아니며, 단일 저장 구조다.

## 5. 상태 객체 스키마

### 5.1 최상위 세션 상태

```json
{
  "version": 6,
  "runId": "run-abc123",
  "grimoireId": "1000002",
  "balanceSettings": {},
  "currentPage": 1,
  "overrunLevel": 0,
  "completedBooks": 0,
  "phase": "battle",
  "pagePlan": [],
  "player": {},
  "battle": {},
  "reward": null,
  "shop": null,
  "ending": null,
  "runRewardClaimed": false,
  "retirementSummary": null,
  "log": []
}
```

추가 필드 의미:

- `runId`
  - 런 식별용 런타임 ID
- `overrunLevel`
  - 현재 런이 몇 번째 오버런 세그먼트까지 진입했는지
- `completedBooks`
  - 현재 런에서 완전히 봉인한 책 세그먼트 수
- `runRewardClaimed`
  - 현재 런이 이미 메타 정산을 마쳤는지 여부
- `retirementSummary`
  - 마지막 정산 결과 요약. 엔딩 화면과 도서관 안내에서 재사용된다.

### 5.2 `phase` 종류

- `library`
- `battle`
- `shop`
- `reward`
- `ending`

### 5.3 플레이어 상태

```json
{
  "hp": 60,
  "maxHp": 60,
  "mp": 2,
  "maxMp": 8,
  "gold": 90,
  "shield": 0,
  "collapse": 0,
  "statuses": [],
  "artifacts": [],
  "handSlots": {
    "aces": { "locked": false, "element": "None", "powerLevel": 0 }
  }
}
```

핵심 포인트:

- `artifacts`는 아티팩트 ID 배열이다.
- `handSlots`는 13개 족보 슬롯에 대한 개별 성장 상태를 가진다.
- 각 족보 슬롯은 다음 3가지를 가진다.
  - `locked`: 현재 페이지에서 사용해 잠겼는지 여부
  - `element`: `None`, `Fire`, `Ice`, `Electric`, `Ground`
  - `powerLevel`: 강화석 누적 레벨

### 5.4 전투 상태

```json
{
  "pageType": "Normal",
  "turn": 1,
  "rerollsLeft": 3,
  "dice": [1, 1, 1, 1, 1],
  "locks": [false, false, false, false, false],
  "rolledThisTurn": false,
  "spellsCastThisTurn": 0,
  "upperSectionScores": {
    "aces": null,
    "deuces": null,
    "threes": null,
    "fours": null,
    "fives": null,
    "sixes": null
  },
  "upperSectionScore": 0,
  "upperSectionTriggered": false,
  "upperBonusDraft": null,
  "monsters": [],
  "defeatedTemplateIds": [],
  "selectedTargetId": null
}
```

### 5.5 보상 상태

```json
{
  "clearedType": "Normal",
  "options": [],
  "selectedRewardId": null,
  "artifactDraft": [],
  "claimedArtifactId": null
}
```

### 5.6 상점 상태

```json
{
  "offers": []
}
```

### 5.7 엔딩 상태

```json
{
  "victory": true,
  "reason": "문자열",
  "canOverrun": true,
  "nextOverrunLevel": 1,
  "nextMonsterMultiplier": 1.08,
  "completionBonus": {
    "title": "문자열",
    "description": "문자열",
    "details": ["문자열"]
  },
  "overrunDraft": {
    "options": [{ "id": "reward-id", "title": "문자열" }],
    "selectedRewardId": null
  }
}
```

패배 엔딩에서는 `reason`만 유지될 수 있다. 승리 엔딩에서는 오버런 가능 여부, 다음 세그먼트의 예상 배율, 이미 적용된 완주 보너스 요약, 그리고 선택 가능한 `오버런 전리품 보관함` 드래프트가 함께 저장될 수 있다.

### 5.8 로그

로그는 최근 14개를 유지한다.

```json
{
  "id": "runtime-id",
  "text": "표시 문자열",
  "tone": "info"
}
```

`tone`은 대략 다음 범주를 가진다.

- `info`
- `success`
- `warning`
- `danger`
- `neutral`

### 5.9 메타 진행 상태

```json
{
  "version": 1,
  "archiveMarks": 0,
  "lifetimePagesCompleted": 0,
  "retiredRuns": 0,
  "clearedRuns": 0,
  "highestCompletedPage": 0,
  "bestOverrunLevel": 0,
  "clearedGrimoireIds": [],
  "selectedLegacyArtifactId": null,
  "lastRetirement": null
}
```

핵심 포인트:

- `archiveMarks`
  - 런 정산 시 누적되는 메타 화폐. 페이지 완료 수와 클리어 보너스로 오른다.
- `selectedLegacyArtifactId`
  - 새 런 시작 시 책 스타터와 함께 추가되는 유산 아티팩트 1개
- `lastRetirement`
  - 가장 최근 런 정산 결과. `archiveMarksGained`, `completedPages`, 신규 해금 목록 등을 담는다.

## 6. 로비와 시작 규칙

### 6.1 기본 밸런스 세팅 값

현재 기본값은 다음과 같다.

- 시작 HP: `60`
- 최대 MP: `8`
- 시작 MP: `2`
- 시작 Gold: `80`
- 시작 Shield: `0`
- 시작 Collapse: `0`
- 몬스터 HP 비율: `43%`
- 상단 합계 보너스 기준값: `55`
- 추가 스타터 아티팩트: 없음

### 6.2 책 선택 시 실제 시작값 계산

플레이어 시작값은 `로비 기본값 + 책 보정치`로 계산한다.

- `maxHp = baseStartHp + grimoire.addHp`
- `maxMp = baseMaxMp + grimoire.addMp`
- `mp = clamp(baseStartMp, 0, maxMp)`
- `gold = baseStartGold + grimoire.addGold`
- `shield = baseStartShield`
- `collapse = baseStartCollapse`

시작 아티팩트는 다음 두 소스를 합친다.

- 책 고유 시작 아티팩트
- 로비에서 추가로 선택한 스타터 아티팩트
- 메타 진행에서 선택한 유산 아티팩트 1개

중복은 제거한다.

### 6.3 밸런스 세팅에서 바꿀 수 있는 항목

- `baseStartHp`
- `baseMaxMp`
- `baseStartMp`
- `baseStartGold`
- `baseStartShield`
- `baseStartCollapse`
- `monsterHpPercent`
- `upperSectionThreshold`
- `extraStarterArtifacts`

### 6.4 해상도 설정

해상도 프리셋은 헤더 드롭다운에서 바꾼다.

- `auto`
- `1366x768`
- `1600x900`
- `1920x1080`

창 최소 크기는 `1280x720`이다.

### 6.5 마도서 정체성 메타

현재 마도서 데이터는 단순 수치 세트가 아니라, 책 선택 화면에서 전략 방향을 설명하기 위한 메타 필드를 함께 가진다.

- `subtitle`
  - 책의 한 줄 분류
- `mood`
  - 책의 전반적인 플레이 감각
- `playstyle`
  - 이 책이 요구하는 운영 축
- `openingPlan`
  - 초반 3~5페이지 운영 가이드
- `caution`
  - 이 책에서 자주 발생하는 실패 패턴 또는 주의점
- `identityTags`
  - 책 카드에 칩 형태로 노출되는 핵심 태그 배열
- `signatureRuleTitle`
  - 책의 고유 규칙 이름
- `signatureRuleTiming`
  - 규칙이 발동하는 타이밍 요약
- `signatureRuleDescription`
  - 고유 규칙의 실제 효과 설명
- `completionBonusTitle`
  - 책을 완주했을 때 적용되는 보너스 이름
- `completionBonusDescription`
  - 완주 보너스 효과 요약
- `completionBonusEffects`
  - `resourceBundle`, `upgradeHands`, `infuseHands` 조합으로 구성된 실제 적용 규칙

즉, 현재 구현의 마도서는 `시작 스탯 + 스타팅 유물 + 전략 요약`의 묶음으로 취급한다.

대표 마도서 5권에 더해, 해금 마도서 7권(`1100001`, `1100002`, `1200003`, `1200004`, `1200005`, `1200006`, `1200010`)도 실제 상태 전이에 영향을 주는 고유 규칙을 가진다.

- `1000001`
  - `안정된 서문`
  - 1~5페이지 전투 시작 시 Shield +6, MP +1
- `1000002`
  - `정리된 목차`
  - Boss / FinalBoss 보상에서 유물 드래프트 4개
- `1000003`
  - `금단의 주해`
  - 매 전투 시작 시 Collapse +6, MP +2
- `1000004`
  - `실험 메모`
  - 일반 / 보스 보상에 정령석 선택지 1개 추가
- `1000005`
  - `추적 교본`
  - 적이 2마리 이상인 전투에서는 매 턴 리롤 +1
- `1100001`
  - `순찰 노획품`
  - 일반 / 보스 보상에 강화석 선택지 1개 추가
- `1100002`
  - `역행 필사`
  - 매 전투에서 처음으로 MP 비용 4 이상 주문을 족보 일치로 시전하면 MP +2 즉시 환급
- `1200003`
  - `장막 정렬`
  - 턴 시작 시 보호막이 10 이상이면 MP +1, 리롤 +1
- `1200004`
  - `태엽 예열`
  - 턴 종료 시 리롤을 2회 이상 남기면 다음 턴 시작에 MP +1, Shield +4
- `1200005`
  - `재점화 교정`
  - 매 턴 처음으로 족보 일치 주문을 적중시키면 화상 부여, 이미 화상 상태 적이 포함돼 있었다면 MP +1 및 남은 적에게 화상 확산
- `1200006`
  - `서리 추적선`
  - 매 턴 처음으로 족보 일치 주문을 적중시키면 빙결 부여, 턴 시작 시 빙결 상태 적이 남아 있으면 Shield +4, 이미 빙결된 적을 족보 일치 주문으로 다시 맞히면 추가 냉기 4 피해
- `1200007`
  - `잔류 방전`
  - 족보 일치 주문이 적중하면 감전 부여, 이미 감전 상태 적이 포함돼 있었다면 남은 적에게 전격 잔광 8 피해, 남은 적이 없으면 해당 표적에 전하 환류 8 추가 피해. 이때 보스/최종보스는 현재 감전 누적량에 비례한 `보스 과전류`를 최대 6까지 추가로 받고, 같은 타이밍에 `전하 차폐` Shield +4를 돌려받음
- `1200008`
  - `압인 붕락`
  - 족보 일치 주문이 적중하면 쇄약 부여, 이미 쇄약 상태 적이 포함돼 있었다면 대상의 남은 보호막을 8 더 파쇄하고 보호막이 비어 있는 표적에는 `붕락` 6 추가 피해를 HP에 직접 밀어 넣음. 남은 적이 따로 없을 때는 현재 쇄약 누적량에 따라 이 붕락이 최대 12 피해까지 깊어지고, 그 구간이 보스/최종보스라면 `균열 차폐` Shield +4를 즉시 되돌려 줌. 다수전에서는 가장 위협적인 비대상 생존 적에게 `붕락 잔진` 4~6 직접 피해를 넘김. 최근 `Ground Collapse Timing Report` 기준 HP 100 · 100회 단일 검증에서는 페이지 5 / 10 / 15 평균 잔진 피해가 0.89 / 2.80 / 5.12, 평균 완충량이 8.97 / 21.60 / 36.14 Shield, 보스 직접 환전 비중이 7.1% / 2.3% / 1.4%로 집계돼 단일보스 생존 전환은 개선됐지만 실패 사유는 여전히 HP 고갈이 주류였음
- `1200009`
  - `종막 추적선`
  - 매 전투 첫 HP 50% 이하 진입 대상에게 MP +1, Shield +4, 그 대상이 보스 / 최종 보스라면 MP +1, Shield +2를 추가로 얻고 이후 HP 50% 이하 보스를 적중시키는 족보 일치 주문마다 추격 12 피해
- `1200010`
  - `초교정 여백`
  - 매 전투에서 처음으로 족보 불일치 주문을 시전하면 해당 족보 잠금을 1회 무시

모든 마도서는 책 완주 시 즉시 적용되는 `완주 보너스`도 함께 가진다.

- 보너스 종류
  - 자원 묶음: HP / MP / Shield / Gold / Collapse 증감
  - 족보 강화: 무작위 족보 `powerLevel` 증가
  - 속성 주입: 무작위 족보에 특정 속성 부여
- 예시
  - `1000001`
    - `평온한 에필로그`
    - HP +14, Shield +10, Collapse -8
  - `1000002`
    - `정리 수당`
    - Gold +45, 무작위 족보 2개 강화
  - `1200005`
    - `화염 잔열`
    - 무작위 족보 2개에 Fire 속성 부여, Collapse -8

### 6.6 서고 기록과 해금 규칙

- 런 종료 시 현재 진행도에 따라 `서고 기록`이 지급된다.
- 기본 지급량은 완료한 페이지 수와 동일하다.
- 여기에 봉인 완료한 책 세그먼트 수마다 추가 보너스 `+5`가 붙는다.
- 이 정산은 패배, 수동 종료, 클리어 엔딩 모두에서 한 번만 수행된다.

현재 메타 해금은 다음 축으로 나뉜다.

- 마도서 해금
  - 기본 6권은 처음부터 열려 있다.
  - 이후 책은 특정 서고 기록 수치에 도달하면 해금된다.
- 유산 기능 해금
  - 서고 기록 `12` 이상이면 `서고 유산` 기능이 열린다.
  - 이후 해금된 유산 아티팩트 중 1개를 새 런 시작 전에 선택할 수 있다.
- 유산 아티팩트 해금
  - 특정 서고 기록 구간마다 고정 유산 아티팩트가 열린다.
  - 이 아티팩트는 일반 런 중 획득과 별도로, 시작 장비처럼 새 런에 들고 갈 수 있다.

현재 해금 곡선은 초반 체감 다양성을 빠르게 열어 주도록 앞구간을 더 촘촘하게 배치했다.

- `12`
  - `1100001` 해금
  - `70008` 해금
  - `legacy-loadout` 해금
- `18`
  - `1100002` 해금
- `20`
  - `70043` 해금
- `24`
  - `1200001` 해금
- 이후에는 대체로 8~16 기록 간격으로 새 책이나 유산이 열린다.

## 7. 전투 핵심 규칙

### 7.1 턴 흐름

1. 턴 시작 처리
2. MP 회복, Shield 감소, 상태이상 턴 시작 처리
3. 리롤 횟수 설정
4. 몬스터 intent 갱신
5. 플레이어가 주사위를 굴림
6. 잠글 주사위를 선택
7. 발동 가능한 족보 1개를 선택하거나 턴을 넘긴다
8. 주문 피해 및 상태이상 적용
9. 킬 보상과 상단 합계 보너스 체크
10. 붕괴 발동 체크
11. 몬스터 턴 실행
12. 다음 턴 시작

### 7.2 턴 시작 MP 규칙

기본적으로 턴 시작 시 `2 MP`를 회복한다.

여기에 다음이 더해진다.

- 붕괴 단계 보너스 `mpRegenBonus`
- 아티팩트의 `turnStartMp`

단, 전투 첫 턴은 예외다.

- 첫 턴에는 기본 `+2 MP`를 즉시 더하지 않는다.
- 전투 시작 시 이미 보유한 시작 MP를 그대로 사용한다.
- 대신 `turnStartMp`형 아티팩트 보너스만 적용된다.

### 7.3 턴 시작 Shield 규칙

- 첫 턴이 아니면 턴 시작 시 기존 Shield가 `2` 감소한다.
- 이후 아티팩트 `turnStartShield`가 더해진다.

### 7.4 리롤 규칙

기본 리롤 수는 `3`이다.

최종 리롤 수는 다음 합으로 계산된다.

- 기본 `3`
- 붕괴 단계 `bonusReroll`
- 아티팩트 `bonusReroll`
- 플레이어 빙결 패널티(`chill`)로 인한 차감

최소값은 1이다.

### 7.5 주사위 규칙

- 6면체 주사위 5개를 사용한다.
- 잠기지 않은 주사위만 새 값으로 굴린다.
- 굴릴 때마다 `rerollsLeft`가 1 감소한다.

### 7.6 잠금 규칙

- 플레이어는 굴린 뒤 일부 주사위를 잠글 수 있다.
- 현재 구현상 각 개별 주사위 lock과 별개로, 족보 슬롯 자체도 잠김 상태를 가진다.
- 족보 슬롯 잠금은 주문 사용 후 발생하며, 페이지를 넘겨야 풀린다.
- 한 플레이어 턴에는 주문을 최대 1회만 사용한다.
- 첫 롤 이후 현재 MP로 사용할 수 있는 잠기지 않은 족보가 하나도 없으면, 턴은 자동으로 몬스터 턴으로 넘어간다.

## 8. 13개 족보 정의

### 8.1 개요

현재 기억 회람판에는 13개의 족보가 있다.

| ID | 이름 | 타겟 | MP | 기본 피해 | 면값 | 판정 family |
| --- | --- | --- | --- | --- | --- | --- |
| aces | 싱글 1 | single | 1 | 5 | 1 | single |
| deuces | 싱글 2 | single | 1 | 5 | 2 | single |
| threes | 싱글 3 | single | 1 | 5 | 3 | single |
| fours | 싱글 4 | single | 1 | 10 | 4 | single |
| fives | 싱글 5 | single | 2 | 10 | 5 | single |
| sixes | 싱글 6 | single | 2 | 10 | 6 | single |
| triple | 트리플 | aoe | 2 | 20 | - | triple |
| four | 포 | aoe | 3 | 28 | - | four |
| full-house | 풀하우스 | single | 3 | 30 | - | fullHouse |
| small-straight | 스몰 스트레이트 | aoe | 3 | 32 | - | smallStraight |
| big-straight | 빅 스트레이트 | aoe | 4 | 40 | - | bigStraight |
| chance | 찬스 | single | 2 | 0 | - | chance |
| yacht | 야추 | aoe | 5 | 50 | - | yacht |

### 8.2 판정 수식

#### 싱글 1~6

- 해당 면이 1개 이상 나오면 성공
- 기본 피해 = `나온 개수 * slotDamage`
- 상단 점수 = `나온 개수 * face`

예:

- `fours`에서 4가 2개 나오면 기본 피해 `20`, 상단 점수 `8`

#### 트리플

- 같은 숫자 3개 이상
- 기본 피해 = `max(20, 주사위 총합)`
- 광역

#### 포

- 같은 숫자 4개 이상
- 기본 피해 = `max(28, 주사위 총합 + 6)`
- 광역

#### 풀하우스

- `3개 + 2개` 조합
- 기본 피해 = `30 + floor(총합 / 2)`
- 단일

#### 스몰 스트레이트

- `1,2,3,4` 또는 `2,3,4,5` 또는 `3,4,5,6`
- 기본 피해 = `32`
- 광역

#### 빅 스트레이트

- `1,2,3,4,5` 또는 `2,3,4,5,6`
- 기본 피해 = `40`
- 광역

#### 찬스

- 항상 성공
- 기본 피해 = `주사위 총합`
- 단일

#### 야추

- 같은 숫자 5개
- 기본 피해 = `50`
- 광역

### 8.3 족보 슬롯 강화

강화석으로 `powerLevel`이 올라간다.

- 1레벨당 추가 기본 피해: `+4`
- 실제 추가값 = `powerLevel * 4`

즉, 어떤 족보든 판정 성공 시 기본 피해에 강화분이 더해진다.

## 9. 비용 감소와 MP 규칙

### 9.1 기본 MP 비용

각 족보 카드의 `cost`를 사용한다.

### 9.2 실제 비용 계산

실제 비용은 다음 함수로 구해진다.

- `currentHandCost = max(0, baseCost - costReduction)`

현재 `costReduction`을 주는 대표 아티팩트는 다음 계열이다.

- `firstCastDiscount`

현재 구현은 “이번 턴 첫 주문”에 대해 비용을 줄인다.

### 9.3 MP 부족 시

- 주문 시도는 실패한다.
- 로그에 경고를 남긴다.
- 행동은 소비되지 않는다.
- 단, 첫 롤 이후 잠기지 않은 족보 중 어떤 것도 현재 MP로 사용할 수 없으면 플레이어 턴은 자동 종료된다.

### 9.4 족보 불일치 시

- 기본 피해는 `0`
- 그러나 비용은 먼저 지불된다.
- 슬롯은 잠긴다.
- 단, `refundOnMiss` 아티팩트가 있으면 MP가 일부 환급된다.

## 10. 피해 공식

### 10.1 기본 공식

실제 피해는 아래 순서로 계산된다.

1. 족보 판정으로 `matchInfo.baseDamage`를 구한다.
2. 여기에 족보 강화분 `powerLevel * 4`를 더한다.
3. 아티팩트 및 붕괴 단계 보너스를 모두 합산해 총 배율을 만든다.
4. 속성 상성 배율을 곱한다.
5. 감전/쇄약 등으로 증가한 피격 배율을 곱한다.
6. 내림(`floor`) 처리한다.

### 10.2 총 배율 구성

최종 공식은 개념적으로 다음과 같다.

```text
finalDamage =
floor(
  (baseDamage + powerBonus)
  * (1
      + artifact.damageMultiplier
      + artifact.collapseBonusMultiplier
      + artifact.infusedHandMultiplier
      + artifact.elementMultiplier
      + artifact.bossDamageMultiplier
      + artifact.statusedEnemyMultiplier
      + collapseTier.damageBonus)
  * affinityMultiplier
  * statusDamageTakenMultiplier
)
```

### 10.3 Shield 처리

- 피해는 먼저 Shield가 흡수한다.
- 남은 값만 HP에 들어간다.

## 11. 속성과 상성

### 11.1 속성 종류

- `None`
- `Fire`
- `Ice`
- `Electric`
- `Ground`

### 11.2 상성 배율

- 무속성 또는 상대 무속성: `1.0`
- 동일 속성: `0.8`
- 유리 상성:
  - Fire -> Ice
  - Ice -> Ground
  - Electric -> Fire
  - Ground -> Electric
- 유리 상성 배율: `1.2`
- 그 외: `1.0`

## 12. 상태이상 시스템

### 12.1 상태이상 종류

- `burn` 화상
- `chill` 빙결
- `shock` 감전
- `fracture` 쇄약
- `rage` 격노

### 12.2 상태 데이터 구조

```json
{
  "id": "burn",
  "stacks": 1,
  "duration": 2
}
```

### 12.3 화상

- 턴 시작 시 피해
- 기본 피해량: `stacks * 4`
- 몬스터가 받는 화상 피해는 플레이어 아티팩트 `burnDamageBonus`의 영향을 받는다.

### 12.4 빙결

- 플레이어에게 걸리면 리롤 패널티
- 몬스터에게 걸리면 공격력 감소
- 몬스터 실효 공격력 공식:
  - `attack + rage*2 - chill*2`

### 12.5 감전

- 받는 피해 증가
- 배율:
  - `1 + shockStacks * 0.2`

### 12.6 쇄약

- 받는 피해 증가
- 시작 시 보호막 파괴
- 피해 증가 배율:
  - `1 + fractureStacks * 0.1`
- 턴 시작 시 Shield가 있으면 `fractureStacks * 2`만큼 감소

### 12.7 격노

- 몬스터 공격력 증가
- 실효 공격력에 `stacks * 2` 더한다.

### 12.8 속성 주입에 따른 상태 적용

플레이어가 속성 주입된 족보를 적중시키면 다음 효과를 부여한다.

- Fire -> burn
- Ice -> chill
- Electric -> shock
- Ground -> fracture

기본값:

- 중첩: 1
- 지속: 2턴

이 값은 아티팩트로 증가할 수 있다.

### 12.9 몬스터 원소 공격

몬스터도 자기 속성에 맞는 상태를 플레이어에게 건다.

- 기본 지속은 2턴
- `burn` intent로 공격한 경우 추가 중첩 1, 지속 1 증가

## 13. 붕괴 시스템

### 13.1 개요

붕괴는 리스크와 파워가 함께 올라가는 게이지다.

- 높아질수록 MP 회복량과 피해 배율이 증가한다.
- 너무 높아지면 패배한다.
- 턴 종료 시 확률적으로 추가 상승할 수 있다.

### 13.2 붕괴 단계표

| 단계 | 구간 | MP 회복 보너스 | 피해 보너스 | 추가 리롤 |
| --- | --- | --- | --- | --- |
| 정적 | 0-19 | 0 | 0% | 0 |
| 요동 | 20-39 | 1 | 0% | 0 |
| 균열 | 40-59 | 2 | 8% | 0 |
| 호전 | 60-79 | 3 | 20% | 0 |
| 격류 | 80-89 | 4 | 32% | 0 |
| 과부하 | 90-100 | 4 | 32% | 1 |

### 13.3 턴 종료 붕괴 발동 확률

확률 공식:

```text
chance = clamp(0.07 + collapse * 0.002 - collapseChanceReduction, 0.04, 0.55)
```

발동 시:

- 붕괴 +7

### 13.4 붕괴 감소

몬스터 처치 시 `collapseOnKill`만큼 감소한다.

보너스 효과나 일부 보상도 붕괴를 낮출 수 있다.

### 13.5 패배 조건

- 붕괴 `>= 100`

## 14. 상단 합계 보너스 시스템

### 14.1 개요

상단 1~6 족보의 점수를 전투 단위로 따로 추적해서, 기준값 이상이 되면 그 전투에서 1회 한정 보너스 드래프트가 열린다.

### 14.2 누적 방식

- `aces ~ sixes`만 대상
- 족보를 쓸 때마다 해당 족보의 상단 점수를 기록한다.
- 족보가 불일치면 해당 칸은 `0`으로 기록된다.
- 이미 기록된 값을 다시 쓰면 덮어쓴다.
- 총합 = 6개 항목의 합

### 14.3 발동 조건

- 아직 발동하지 않았고
- `upperSectionScore >= upperSectionThreshold`
- 주문 해석 뒤 `checkDefeat -> handleBattleClearAfterKills -> maybeTriggerUpperSectionBonus` 순서로 검사한다.
- 따라서 패배나 전투 종료가 먼저 확정되면 드래프트는 열리지 않는다.

현재 기본 기준값은 `55`다.

### 14.4 드래프트 구조

- 보너스 라이브러리 10종 중 무작위 3종 노출
- 1개 선택
- 선택 즉시 적용
- 드래프트가 떠 있는 동안 Roll / Lock / 주문 선택 / Pass 입력은 막힌다.
- 선택 후 `resolveKilledMonsters -> checkDefeat -> handleBattleClearAfterKills -> finalizeBattleAction` 순서로 이어진다.

### 14.5 보너스 효과 10종

보너스 ID 기준으로 정리하면 다음과 같다.

1. `upper-restorative-ink`
   - HP `+16`, MP `+2`
2. `upper-sigil-barrage`
   - 모든 생존 적에게 `18` 피해
3. `upper-gilded-footnote`
   - Gold `+35`
4. `upper-warded-cover`
   - Shield `+12`
5. `upper-calming-ribbon`
   - 붕괴 `-12`
6. `upper-clear-mind`
   - 플레이어의 부정 상태이상 제거 + HP `+8`
7. `upper-fracture-wave`
   - 모든 생존 적에게 `fracture 1 / 2턴`
8. `upper-reopened-margin`
   - 잠긴 족보 1개 무작위 해제
   - 잠긴 족보가 없다면 MP `+2`
9. `upper-mana-surge`
   - MP `+4`
10. `upper-execution-note`
   - 현재 HP가 가장 높은 적에게 `max(24, floor(target.maxHp * 0.18))`
   - Shield 무시
   - 대상이 없으면 Gold `+20`

## 15. 몬스터 생성과 스케일링

### 15.1 몬스터 생성식

`createMonster(template, page, grimoire, balanceSettings, role)` 기준.

#### HP 스케일

- 일반 적:
  - `hpScale = floor((page - 1) / 2)`
- 보스:
  - `hpScale = max(2, floor(page * 0.55))`
- 최종 보스:
  - `hpScale = max(4, floor(page * 0.65))`

기본 HP:

```text
baseHp = template.baseHp + hpScale * template.hpGrowth + grimoire.addMonsterHp
```

역할별 HP 배율:

- 일반:
  - page < 10: `1.0`
  - page 10~19: `0.9`
  - page >= 20: `0.82`
- 보스:
  - page < 20: `0.72`
  - page >= 20: `0.66`
- 최종 보스:
  - `0.58`

최종 HP:

```text
adjustedHp = round(baseHp * hpMultiplier * (monsterHpPercent / 100))
```

추가 바닥값:

- 일반 적이고
- 책의 몬스터 HP 보정이 0 이상일 때
- page <= 2: 최소 `32`
- page <= 4: 최소 `34`

### 15.2 공격력 스케일

- 일반 적:
  - `attackScale = floor((page - 1) / 3)`
- 보스/최종 보스:
  - `attackScale = floor(page / 2)`

기본 공격력:

```text
baseAttack = template.baseAttack + attackScale * template.attackGrowth + grimoire.addMonsterAttack
```

배율:

- 일반:
  - page < 10: `1.0`
  - page 10~19: `0.96`
  - page >= 20: `0.92`
- 보스:
  - page < 20: `0.84`
  - page >= 20: `0.8`
- 최종 보스:
  - `0.72`

최종 공격력:

```text
attack = round(baseAttack * attackMultiplier)
```

### 15.3 스폰 직후 HP 표시 규칙

신규 스폰 몬스터는 반드시 `hp == maxHp`로 보이도록 정규화한다.

구버전 세이브에서 `maxHp`만 남아 어색하게 보이는 문제를 막기 위해, 전투 초반 상태에서는 `maxHp`를 `hp`에 맞춰 보정한다.

### 15.4 특수 몬스터 훅

일부 몬스터 템플릿은 공통 스탯 외에 다음 훅을 추가로 가진다.

- `damageTakenMultiplier`
  - 주문 피해에 곱해지는 배율. `서고 박쥐`처럼 특정 적만 피해 경감 패시브를 가진다.
- `shieldedDamageTakenMultiplier`
  - 현재 보호막이 1 이상일 때만 추가로 곱해지는 주문 피해 배율. `기록 골렘`처럼 `방벽을 먼저 벗겨야 본딜이 들어가는` 적을 만들 때 사용한다.
- `playerCollapseAttackStep`, `playerCollapseAttackGain`
  - 플레이어 붕괴 수치에 반응해 몬스터 공격력을 올리는 훅. 현재는 `붕락 정령`이 `붕괴 10%마다 공격 +1` 규칙으로 사용한다.
- `deathBurstBaseDamage`, `deathBurstCollapseStep`, `deathBurstDamageGain`
  - 몬스터 처치 직후 플레이어에게 남기는 사망 폭발 피해 훅. 보호막이 먼저 흡수하며, `붕락 정령`은 `기본 2 + 붕괴 20%마다 +1` 공식을 사용한다.
- `healOnHitRatio`
  - 공격으로 플레이어 HP에 실제로 들어간 피해 비율만큼 자신이 회복한다.
- `reviveChance`
  - 사망 시 1회 부활 확률.
- `reviveHpRatio`
  - 부활 성공 시 회복하는 최대 HP 비율.
- `onHitStatuses`
  - 공격 적중 시 플레이어에게 추가로 거는 상태이상 배열. 각 항목은 `{ id, stacks, duration }` 구조를 쓴다.
- `singleTargetImmuneTurns`
  - 전투 시작 후 지정한 턴 수 동안 `single` 타입 주문 피해를 0으로 만든다. 현재는 `잿빛 와이번`이 1턴 값을 사용한다.
- `untouchedAttackGainPercent` / `bonusAttackPercent`
  - 플레이어 턴 동안 HP 피해를 받지 않았을 때 다음 행동 직전 공격력을 누적 상승시키는 수치. `effectiveMonsterAttack()` 계산에 직접 반영된다.

현재 구현 예시:

- `서고 박쥐`
  - `damageTakenMultiplier = 0.5`
  - `healOnHitRatio = 0.5`
- `금간 해골`
  - `reviveChance = 0.5`
  - `reviveHpRatio = 0.1`
  - `onHitStatuses = [chill 1/2턴, fracture 1/2턴]`
- `잿빛 와이번`
  - `singleTargetImmuneTurns = 1`
  - `untouchedAttackGainPercent = 0.1`
  - 플레이어 턴 동안 HP 피해를 받지 않으면 몬스터 행동 직전 `bonusAttackPercent += 0.1`
- `기록 골렘`
  - `shieldedDamageTakenMultiplier = 0.75`
  - `onHitStatuses = [fracture 1/2턴]`
  - 보호막이 남아 있는 동안 주문 피해를 25% 덜 받고, 공격 적중 시 쇄약을 남겨 다음 턴 압박을 키운다.
- `붕락 정령`
  - `playerCollapseAttackStep = 10`, `playerCollapseAttackGain = 1`
  - `deathBurstBaseDamage = 2`, `deathBurstCollapseStep = 20`, `deathBurstDamageGain = 1`
  - 플레이어가 쌓아 둔 붕괴 수치를 그대로 화력과 사망 폭발로 되돌려 준다.

## 16. 몬스터 행동 의도(intent)

### 16.1 intent 원천

각 몬스터는 템플릿에 `intents` 배열을 가진다.

예:

- `attack`
- `defend`
- `guard`
- `collapse`
- `heal`
- `spawn`
- `buff`
- `burn`

### 16.2 의도 갱신 시점

매 플레이어 턴 시작 시 모든 생존 몬스터의 `currentIntent`를 새로 생성한다.

### 16.3 특수 규칙

몬스터 HP가 `35% 이하`이고, intent 풀에 `heal`이 포함돼 있으면 회복 의도를 우선 사용할 수 있다.

### 16.4 intent별 효과

- `attack`
  - 플레이어에게 실효 공격력만큼 피해
  - `onHitStatuses`가 있으면 적중 후 추가 상태이상을 함께 부여
- `guard` / `defend`
  - 자기 Shield `+6`
- `collapse`
  - 플레이어 붕괴 `+7`
- `heal`
  - 자기 HP `+16`
- `spawn`
  - 활성 몬스터가 3 미만이면 같은 테마 일반 적 1마리 소환
  - 이미 3마리면 대신 Shield `+4`
- `buff`
  - 자기 자신에게 `rage 1 / 3턴`
- `burn`
  - 일반 공격보다 `+2` 높은 피해
  - 속성 상태이상도 적용

## 17. 승리와 패배

### 17.1 승리

- 현재 페이지가 마지막 페이지이고
- 모든 적을 처치하면
- `ending.victory = true`

### 17.2 패배

아래 중 하나면 패배:

- 플레이어 HP `<= 0`
- 붕괴 `>= 100`
- 모든 족보 슬롯이 잠김

### 17.3 페이지 종료 후 잠금 해제

보상 화면에서 다음 페이지로 이동할 때:

- 모든 족보 슬롯 잠금을 해제한다.

## 18. 보상 시스템

### 18.1 일반 스테이지 보상

기본적으로 3개가 고정 생성된다.

1. 정령석 1개
2. 강화석 1개
3. 체력 회복 1개

late normal/오버런 normal 페이지에서는 여기에 `책 테마 전리품` 1개가 추가되어 기본적으로 4택 1이 된다.

- slime 테마: `HP +8, Shield +8, Collapse -8`
- goblin 테마: `Gold +35 + 무작위 족보 1개 강화`
- lich 테마: `MP +2 + 무작위 족보 1개 속성 주입`

또한 같은 구간에서 `기록 골렘(420016)` 또는 `붕락 정령(420017)`을 실제로 처치했다면, 보상 후보에 해당 적 전리품이 1장 더 섞여 최대 5택 1이 된다.

- 기록 골렘 처치 전리품 `균열 석판 조각`
  - `Shield +12`
  - 무작위 족보 1개 강화
  - 무작위 족보 1개에 `Ground` 속성 주입
- 붕락 정령 처치 전리품 `붕락 응축핵`
  - `MP +2`
  - `Collapse -12`
  - 무작위 족보 1개에 `Electric` 속성 주입

오버런에서는 같은 적 전리품이 `+` 버전으로 떠서 수치가 소폭 강화된다. 내부적으로는 전투 중 실제로 마무리한 몬스터의 `templateId`를 `battle.defeatedTemplateIds`에 누적하고, 전리품 정의가 있는 특수 적은 세그먼트 단위 누적 목록 `segmentTrophyTemplateIds`에도 기록한다. reward 생성 시 전자는 해당 전투 보상 후보를, 엔딩의 `오버런 전리품 보관함` 생성 시 후자는 책 클리어 직후 드래프트 후보를 만드는 데 사용된다.

즉 후반 일반전부터는 현재 책이 던지는 위협 결과 `방금 쓰러뜨린 적의 잔해`가 곧바로 다음 보상 기대와 연결되고, 책 클리어 후에는 그중 일부를 직접 골라 다음 오버런 준비물로 다시 들고 갈 수 있다.

### 18.2 보스 스테이지 보상

일반 보상 3개는 동일하다.

추가로:

- 아티팩트 드래프트 3개 생성
- 그 중 1개를 획득해야 다음 페이지로 넘어갈 수 있다.
- 단, 전부 `매진`이면 아티팩트 선택 없이 진행 가능

### 18.3 정령석 스폰 규칙

타깃 수 결정:

- 단일 타깃: `80`
- 다중 타깃: `20`

다중 타깃일 때 개수:

- 2개: `70`
- 3개: `25`
- 4개: `4`
- 5개: `1`

정령석은 속성을 4개 중 하나에서 무작위 선택한다.

- Fire
- Ice
- Electric
- Ground

### 18.4 정령석 적용 규칙

N개 타깃을 고를 때 우선순위는 다음과 같다.

1. 속성이 없는(`None`) 족보 슬롯을 무작위로 먼저 채운다.
2. 부족하면 이미 속성이 있는 슬롯을 무작위로 골라 덮어쓴다.

### 18.5 강화석 규칙

타깃 수는 정령석과 같은 규칙을 쓴다.

적용 시:

- 13개 족보 중 중복 없이 무작위 N개를 골라
- 각 슬롯의 `powerLevel += 1`

실질 효과:

- 슬롯당 기본 피해 `+4`

### 18.6 회복 보상 규칙

회복량:

```text
maxHp * (3% ~ 8%) + missingHp * (15% ~ 30%)
```

제한:

- 최대 `maxHp의 35%`
- 최종값은 반올림 후 최소 1 보장

## 19. 아티팩트 보상 스폰 규칙

### 19.1 슬롯 수

- 3개

### 19.2 중복 방지

아래는 스폰 대상에서 제외한다.

- 이미 플레이어가 보유한 아티팩트
- 같은 드래프트 내 다른 슬롯에 이미 뽑힌 아티팩트

### 19.3 희귀도 가중치

현재 구현 가중치:

- Common: `50`
- Rare: `30`
- Epic: `15`
- Heroic: `4`
- Legendary: `1`

`Uncommon` 대신 현재 코드베이스의 등급 체계인 `Rare`를 사용한다.

### 19.4 고갈 처리

- 특정 희귀도에 더 이상 뽑을 아티팩트가 없으면 그 희귀도는 즉시 추첨 테이블에서 빠진다.
- 모든 아티팩트가 고갈되면 해당 슬롯은 `Sold Out` 카드가 된다.

## 20. 상점 시스템

### 20.1 상점 슬롯 구조

현재 구현은 4칸 고정이다.

- 아티팩트 2칸
- 정령석 1칸
- 강화석 1칸

### 20.2 상점 아티팩트 스폰

상점 아티팩트도 보상 드래프트와 동일한 가중치를 사용한다.

- Common 50
- Rare 30
- Epic 15
- Heroic 4
- Legendary 1

보유 중복과 동일 상점 내 중복도 막는다.

### 20.3 상점 가격

#### 아티팩트

- 데이터상의 `price` 사용
- 단, `shopDiscount` 아티팩트 합산만큼 할인
- 최저가는 `25G`

#### 정령석

```text
50 + (targetCount - 1) * 20
```

#### 강화석

```text
55 + (targetCount - 1) * 18
```

### 20.4 구매 규칙

- Gold가 부족하면 구매 불가
- 구매 후 즉시 효과 적용
- 같은 상점 카드 재구매 불가

## 21. 아티팩트 시스템

### 21.1 개요

아티팩트는 런 동안 영구 유지되는 패시브 효과다.

- 총 수량: `61개`
- 희귀도:
  - Common 11
  - Rare 23
  - Epic 14
  - Heroic 7
  - Legendary 6

### 21.2 주요 효과 카테고리

아티팩트는 대략 다음 카테고리로 나뉜다.

- 특정 눈값 포함 시 피해 증가
- 전체 주문 피해 증가
- 추가 리롤
- 속성별 피해 증가
- 킬 골드 보너스
- 속성 주입 족보 피해 증가
- 붕괴 확률 제어
- 첫 주문 MP 할인
- 턴 시작 Shield
- 광역/단일 주문 보너스
- 특정 족보군 보너스
- 고비용 주문 보너스
- 보호막 조건부 보너스
- 킬 시 MP 회복
- 저체력 조건부 보너스
- 처형 조건부 보너스
- 족보 불일치 환급
- 상태이상 강화
- 상태이상 대상 추가 피해
- 화상 지속 피해 강화
- 보스 대상 추가 피해
- 저붕괴 조건부 보너스
- 턴 시작 MP
- 상점 할인
- 족보 일치 시 Shield

### 21.3 아티팩트 집계 방식

엔진은 전투 시점마다 `artifactTotals(state, context)`를 계산한다.

`context`에 따라 다음 값들이 달라진다.

- 현재 주사위 값
- 현재 슬롯의 속성
- 족보 family
- 단일/광역 여부
- 주문 비용
- 남은 리롤 수
- 보스 대상 여부
- 적 상태이상 목록
- 족보 일치 여부

즉, 아티팩트는 항상 고정 버프가 아니라 문맥 기반 조건부 버프일 수 있다.

## 22. 오디오 시스템

### 22.1 BGM

현재 BGM 트랙은 6개다.

- `library`
- `battleNormal`
- `battleBoss`
- `shop`
- `endingClear`
- `endingFail`

파일 포맷은 현재 `mp3`다.

### 22.2 SFX

현재 SFX는 30개가량이며, 예시는 다음과 같다.

- UI 클릭
- 오류/거절
- 주사위 굴림
- 잠금 토글
- 주문 발동
- 몬스터 피격
- 플레이어 피격
- 방어막 흡수
- 유물 획득
- 정령석 주입
- 페이지 전환
- 전투 승리
- 런 클리어/실패

### 22.3 오디오 저장값

기본값:

- `bgmEnabled = true`
- `sfxEnabled = true`
- `bgmVolume = 0.42`
- `sfxVolume = 0.78`

## 23. 해상도와 데스크톱 연동

### 23.1 Electron 메인 프로세스 역할

메인 프로세스는 다음 두 가지 IPC를 처리한다.

- `window:set-resolution-preset`
- `balance:run-report`

### 23.2 프리로드 브리지

렌더러에서 `window.dicespellDesktop`을 통해 호출한다.

- `setResolutionPreset(presetId)`
- `runBalanceSimulation(options)`

## 24. 승률 시뮬레이터

### 24.1 목적

실제 엔진을 자동 플레이어 집단으로 반복 실행해 승률과 페이지 도달률을 측정한다.

### 24.2 사용 집단

빌드 사용 성향 3종:

- active
- moderate
- passive

숙련도 4종:

- expert
- intermediate
- junior
- newbie

총 12개 조합을 만든다.

### 24.3 조절 가능한 값

- runs
- monster HP %
- grimoire id
- grimoire ids (쉼표 구분 다중 비교 배치)
- checkpoints (쉼표 구분 체크포인트 페이지 목록)

### 24.4 출력

- Usage Group Average
- Skill Group Average
- Grimoire Average
- Grimoire Usage Average
- Grimoire Skill Average
- Full Matrix
- Page Progress Report
- Checkpoint Diagnostic Report
- Ground Collapse Timing Report (`1200008` 전용 체크포인트 심화 진단)

### 24.5 구현 방식

- 실제 `game-engine.js` API를 호출해 런을 생성한다.
- 자동 플레이 정책으로 굴림/잠금/주문/보상/상점 구매를 수행한다.
- 결과를 집계해 UI와 CLI 둘 다에서 출력한다.
- 체크포인트 진단 리포트는 지정한 페이지에 진입했을 때의 평균 HP / 붕괴 / 유물 수 / 속성 주입 수, 그 페이지에서 바로 탈락한 비율, 주요 실패 사유를 함께 기록한다.
- `Ground Collapse Timing Report`는 `1200008`에 한해 같은 체크포인트 기준으로 `깊은 붕락` 발동 런 비중, 평균 누적 트리거 수, 평균 첫 발동 페이지, 평균 최대 붕락 피해에 더해 `붕락 잔진` 평균 환전량(`AvgRelay<=P`), `균열 차폐` 평균 완충량(`AvgWard<=P`), 보스 직접 환전 비중(`BossRelayShare`)까지 함께 기록해 `발동 빈도`, `환전 경로`, `생존 완충` 가설을 별도 리포트 없이 바로 읽게 한다.

## 25. 구현 시 주의점

### 25.1 소스 문자열 인코딩

현재 코드베이스는 일부 한국어 문자열이 소스 파일이나 터미널 출력에서 깨져 보일 수 있다.

중요한 점:

- 런타임 UI는 대체로 정상 문자열로 동작한다.
- 특히 로그는 `addLog()` 내부에 문자열 정규화 로직이 들어가 있다.
- 따라서 raw source text보다 이 문서와 실제 실행 결과를 우선 신뢰하는 것이 좋다.

### 25.2 죽은 코드(dead code)

반복 수정 과정에서 일부 함수는 상단의 새 구현 아래에 이전 구현이 남아 있다.

대표 예:

- `createRewardPhase`
- `createShopDraft`
- `renderShop`
- `renderReward`
- `buyArtifact`

재구현 시에는 “현재 실제 실행 경로”만 살리고, 죽은 코드는 제거하는 편이 좋다.

### 25.3 소스 오브 트루스 우선순위

재구현 기준 우선순위:

1. 현재 실행 결과
2. 이 역기획서
3. `data.js`와 `game-engine.js`의 실제 수치
4. UI 텍스트

## 26. 복원 체크리스트

Claude가 동일 빌드를 재구현하려면 아래가 모두 맞아야 한다.

- 로비 4탭 구조가 존재한다.
- 책 18권, 족보 13개, 아티팩트 61개가 존재한다.
- 전투 흐름이 `Roll -> Lock -> Spell -> Monster Turn` 구조다.
- 전투 첫 턴 MP 처리와 일반 턴 MP 처리 차이가 있다.
- 붕괴 단계 6구간이 존재한다.
- 상단 합계 보너스 시스템이 존재한다.
- 정령석/강화석/회복 3종 보상 구조가 존재한다.
- 보스 보상에 아티팩트 드래프트가 추가된다.
- 상점이 아티팩트 2 + 정령석 1 + 강화석 1 구조다.
- 족보 슬롯별 `element`, `powerLevel`, `locked` 상태를 가진다.
- 페이지 이동 시 족보 슬롯 잠금이 풀린다.
- 저장 키와 로컬 저장 구조가 동일하다.
- 시뮬레이터가 GUI와 CLI에서 모두 동작한다.

## 27. 부록 A - 책 18권 일람

| ID | 이름 | 페이지 | 테마 | Gold | HP | MP | 몬스터 HP | 몬스터 ATK | 시작 아티팩트 | 요약 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1000001 | 초심자의 봉인서 | 15 | slime | +10 | +0 | +0 | -10 | -2 | - | 낮은 위험과 안정적인 성장 곡선. |
| 1000002 | 중급자의 봉인서 | 30 | goblin | +10 | +0 | +0 | +0 | +0 | - | 기획 기준에 가장 가까운 표준 난이도. |
| 1000003 | 상급자의 봉인서 | 30 | lich | +10 | +0 | +0 | +10 | +2 | - | 더 강한 몬스터와 붕괴 압박을 감당해야 한다. |
| 1100001 | 고블린의 순찰지 | 15 | goblin | +15 | +0 | +0 | +0 | +0 | 70001 | 초기 화력 유물이 붙어 공격 템포가 빠르다. |
| 1100002 | 리치의 일기 | 15 | lich | +15 | +0 | +3 | +0 | +0 | 70007 | 초기 MP 우위로 큰 족보 주문을 빨리 시도할 수 있다. |
| 1000004 | 슬라임 백과사전 | 15 | slime | +10 | +0 | +0 | +0 | +0 | 70011 | 속성 반응을 적극적으로 쓰는 실험형 플레이. |
| 1000005 | 뼈가 있는 말 | 15 | lich | +15 | +0 | +2 | +10 | +10 | 70008 | 더 무거운 적과 맞붙지만 리롤이 넉넉하다. |
| 1000006 | 보물 고블린 장부 | 15 | goblin | +15 | +0 | +0 | +0 | +0 | 70010 | 상점 중심의 성장 루프를 빠르게 굴릴 수 있다. |
| 1200001 | 별빛 연습서 | 15 | slime | +8 | +12 | +0 | +0 | +0 | 70043 | 체력을 온전히 유지할수록 화력이 오르는 정밀 운영용 책. |
| 1200002 | 도매상 거래 원장 | 15 | goblin | +20 | +0 | +0 | +0 | +0 | 70045, 70010 | 상점 유물 가격을 낮추고 골드를 크게 불려 성장하는 상업형 책. |
| 1200003 | 세 겹 장막본 | 15 | lich | +5 | +6 | +0 | +6 | +0 | 70044, 70053 | 족보를 정확히 맞춰 보호막을 쌓고 안정적으로 장기전을 굴린다. |
| 1200004 | 태엽 항해일지 | 30 | goblin | +10 | +0 | +1 | +0 | +0 | 70042, 70008 | 남긴 리롤을 공격력으로 바꾸는 계산형 운영이 중심이 된다. |
| 1200005 | 화염 교정쇄 | 15 | slime | +5 | +0 | +1 | +0 | +0 | 70054, 70024 | 화상 누적과 지속 피해를 적극적으로 밀어붙이는 속성 특화본. |
| 1200006 | 빙결 관측 노트 | 15 | lich | +5 | +4 | +1 | +0 | +0 | 70055, 70025 | 빙결로 몬스터 행동을 약화시키며 안정적으로 템포를 가져온다. |
| 1200007 | 전격 실험지 | 15 | goblin | +5 | +0 | +1 | +0 | +1 | 70056, 70033 | 감전 대상에 폭딜을 집중하는 순간 화력 특화본. |
| 1200008 | 토석 압인본 | 15 | slime | +5 | +10 | +0 | +8 | +0 | 70057, 70027 | 쇄약과 보호막 붕괴를 활용해 단단한 적을 파쇄하고, 단일보스 구간에서는 깊은 붕락으로 마무리를 앞당긴다. 이제 다수전에서도 `붕락 잔진`으로 비대상 생존 적에게 4~6 직접 피해를 넘기고, 보스/최종보스 단일보스 구간에서는 `균열 차폐` Shield +4를 즉시 되돌린다. 최근 `Ground Collapse Timing Report` 기준 페이지 5 / 10 / 15 평균 완충량은 8.97 / 21.60 / 36.14 Shield였다. |
| 1200009 | 종막 사냥 수첩 | 15 | lich | +10 | +0 | +0 | +10 | +2 | 70058, 70060 | 보스 체력을 기준으로 마무리 각을 빠르게 여는 15페이지 보스 러시 책. |
| 1200010 | 오탈자 수습본 | 15 | goblin | +10 | +0 | +2 | +0 | +0 | 70050, 70051 | 첫 주문 할인과 불일치 환급으로 저비용 순환을 빠르게 만든다. |

## 28. 부록 B - 몬스터 템플릿

| 테마 | 역할 | 편성 역할 | ID | 이름 | 속성 | baseHp | hpGrowth | baseAtk | atkGrowth | Gold | 붕괴 감소 | 패시브 | intents |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| slime | normal | 전열 | 400010 | 노멀 슬라임 | None | 15 | 5 | 3 | 1 | 5 | 2 | - | attack, defend |
| slime | normal | 술사 | 400011 | 화염 슬라임 | Fire | 18 | 6 | 4 | 2 | 7 | 2 | - | attack, burn |
| slime | normal | 방벽 | 400012 | 냉기 슬라임 | Ice | 18 | 6 | 3 | 1 | 7 | 2 | - | attack, guard |
| slime | normal | 돌격 | 400013 | 전격 슬라임 | Electric | 16 | 5 | 5 | 2 | 8 | 2 | - | attack, collapse |
| slime | boss | - | 400020 | 슬라임 킹 | None | 220 | 20 | 12 | 3 | 60 | 8 | - | attack, spawn, collapse, guard |
| slime | finalBoss | - | 400021 | 심연의 슬라임 도감 | Ice | 300 | 24 | 14 | 4 | 90 | 12 | - | attack, collapse, collapse, heal |
| goblin | normal | 돌격 | 410003 | 고블린 스카웃 | None | 22 | 5 | 5 | 1 | 8 | 2 | - | attack, defend |
| goblin | normal | 전열 | 410004 | 노말 고블린 | None | 26 | 6 | 6 | 2 | 10 | 3 | - | attack, defend |
| goblin | normal | 방벽 | 410005 | 얼음 고블린 | Ice | 28 | 6 | 7 | 2 | 10 | 3 | - | attack, guard, collapse |
| goblin | normal | 돌격 | 410007 | 전격 고블린 | Electric | 24 | 5 | 8 | 2 | 11 | 3 | - | attack, collapse |
| goblin | boss | - | 410020 | 고블린 대장 | Ground | 250 | 22 | 14 | 3 | 65 | 8 | - | attack, buff, collapse |
| goblin | finalBoss | - | 410030 | 보물 고블린 왕 | Electric | 320 | 24 | 16 | 4 | 100 | 12 | - | attack, buff, collapse, heal |
| lich | normal | 술사 | 420010 | 사골 서기 | Ground | 24 | 6 | 6 | 1 | 10 | 3 | - | attack, collapse |
| lich | normal | 술사 | 420011 | 리치 수행사제 | Fire | 20 | 6 | 8 | 2 | 12 | 3 | - | attack, heal |
| lich | normal | 전열 | 420012 | 무덤 기사 | None | 32 | 7 | 7 | 2 | 12 | 3 | - | attack, guard |
| lich | normal | 교란 | 420013 | 서고 박쥐 | None | 14 | 4 | 7 | 2 | 10 | 2 | 받는 주문 피해 50% 감소, 공격 HP 피해의 50% 흡혈 | attack, attack, burn |
| lich | normal | 돌격 | 420014 | 금간 해골 | None | 12 | 4 | 10 | 3 | 11 | 2 | 사망 시 50% 확률로 최대 HP 10%로 1회 부활, 공격 적중 시 빙결+쇄약 | attack, attack, guard |
| lich | normal | 교란 | 420015 | 잿빛 와이번 | Fire | 18 | 5 | 9 | 2 | 12 | 2 | 전투 첫 턴 단일 주문 피해 무효, 무피해 유지 시 공격력 10% 상승 | attack, burn, attack |
| lich | normal | 방벽 | 420016 | 기록 골렘 | Ground | 30 | 7 | 8 | 2 | 13 | 3 | 보호막 유지 중 주문 피해 25% 감소, 공격 적중 시 쇄약 | guard, collapse, attack |
| lich | normal | 술사 | 420017 | 붕락 정령 | Electric | 16 | 5 | 6 | 2 | 12 | 2 | 플레이어 붕괴 10%마다 공격 +1, 처치 시 현재 붕괴 비례 마나 폭발 | collapse, attack, burn |
| lich | boss | - | 420020 | 기억의 리치 | Ice | 240 | 22 | 15 | 3 | 70 | 8 | - | attack, heal, collapse |
| lich | finalBoss | - | 420030 | 폐허의 기록술사 | Fire | 340 | 25 | 18 | 4 | 110 | 12 | - | attack, heal, collapse, buff |

## 29. 부록 C - 아티팩트 61종 일람

| ID | 이름 | 희귀도 | 가격 | kind | value | 추가 조건 | 효과 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 70001 | 주사위의 문장 I | Common | 65 | diceValueMultiplier | 0.3 | face=1 | 1이 포함될 경우, 1 한 개당 주문 피해 +30%. |
| 70002 | 주사위의 문장 II | Common | 65 | diceValueMultiplier | 0.3 | face=2 | 2가 포함될 경우, 2 한 개당 주문 피해 +30%. |
| 70003 | 주사위의 문장 III | Common | 65 | diceValueMultiplier | 0.3 | face=3 | 3이 포함될 경우, 3 한 개당 주문 피해 +30%. |
| 70004 | 주사위의 문장 IV | Rare | 95 | diceValueMultiplier | 0.3 | face=4 | 4가 포함될 경우, 4 한 개당 주문 피해 +30%. |
| 70005 | 주사위의 문장 V | Rare | 95 | diceValueMultiplier | 0.3 | face=5 | 5가 포함될 경우, 5 한 개당 주문 피해 +30%. |
| 70006 | 주사위의 문장 VI | Rare | 95 | diceValueMultiplier | 0.3 | face=6 | 6이 포함될 경우, 6 한 개당 주문 피해 +30%. |
| 70007 | 역행의 모래시계 | Epic | 120 | globalSpellMultiplier | 0.5 | - | 모든 주문 피해 +50%. |
| 70008 | 잿빛 깃펜 | Rare | 95 | bonusReroll | 1 | - | 턴 시작 시 리롤 +1. |
| 70009 | 전격 술사의 반지 | Epic | 120 | elementMultiplier | 0.3 | element=Electric | 전격 속성 주문 피해 +30%. |
| 70010 | 보물 고블린 장부 | Epic | 120 | bonusGoldOnKill | 4 | - | 몬스터 처치 시 추가 골드 +4. |
| 70011 | 정령석 분광면 | Rare | 95 | infusedHandMultiplier | 0.2 | - | 속성 주입이 된 족보의 피해 +20%. |
| 70012 | 균열 나침반 | Legendary | 145 | collapseControl | 0.2 | - | 붕괴 발생 확률 -20%, 붕괴 60 이상일 때 피해 +20%. |
| 70013 | 교정자의 칼날 | Rare | 95 | firstCastDiscount | 1 | - | 매 턴 첫 주문의 MP 비용 -1. |
| 70014 | 유리 방패 연감 | Common | 65 | turnStartShield | 3 | - | 턴 시작 시 보호막 +3. |
| 70015 | 회오리 주해서 | Rare | 95 | targetTypeMultiplier | 0.25 | targetType=aoe | 광역 주문 피해 +25%. |
| 70016 | 결투가의 서약 | Rare | 95 | targetTypeMultiplier | 0.25 | targetType=single | 단일 주문 피해 +25%. |
| 70017 | 나선 책갈피 | Epic | 120 | handFamilyMultiplier | 0.35 | family=smallStraight+bigStraight | 스트레이트 계열 주문 피해 +35%. |
| 70018 | 심판의 저울 | Epic | 120 | highCostSpellMultiplier | 0.35 | minCost=4 | MP 비용 4 이상 주문 피해 +35%. |
| 70019 | 철벽 낙인 | Rare | 95 | shieldThresholdMultiplier | 0.25 | minShield=4 | 보호막이 4 이상이면 주문 피해 +25%. |
| 70020 | 수확자 갈고리 | Epic | 120 | manaOnKill | 1 | - | 몬스터 처치 시 MP +1 회복. |
| 70021 | 벼랑 끝 계약서 | Legendary | 145 | lowHpMultiplier | 0.3 | hpThreshold=0.5 | HP가 50% 이하일 때 주문 피해 +30%. |
| 70022 | 마침표 인장 | Rare | 95 | executeMultiplier | 0.25 | hpThreshold=0.5 | HP가 50% 이하인 적에게 주문 피해 +25%. |
| 70023 | 오답 지우개 | Common | 65 | refundOnMiss | 1 | - | 족보 불일치 시 MP 1 환급. |
| 70024 | 불씨 촉매 | Common | 65 | elementStatusBoost | 1 | element=Fire | 화염 속성 족보가 부여하는 화상 중첩 +1. |
| 70025 | 서리 필사침 | Common | 65 | elementStatusBoost | 1 | element=Ice | 냉기 속성 족보가 부여하는 빙결 중첩 +1. |
| 70026 | 전하 도체환 | Common | 65 | elementStatusBoost | 1 | element=Electric | 전격 속성 족보가 부여하는 감전 중첩 +1. |
| 70027 | 암반 압인석 | Common | 65 | elementStatusBoost | 1 | element=Ground | 대지 속성 족보가 부여하는 쇄약 중첩 +1. |
| 70028 | 여진 감식판 | Rare | 95 | statusedEnemyMultiplier | 0.2 | - | 상태 이상이 걸린 적에게 주문 피해 +20%. |
| 70029 | 타르 봉인지 | Rare | 95 | burnDamageBonus | 2 | - | 화상 피해가 중첩당 +2 증가. |
| 70030 | 보스 도감 절취본 | Rare | 95 | bossDamageMultiplier | 0.18 | - | 보스와 최종 보스에게 주는 주문 피해 +18%. |
| 70031 | 정숙 교범 | Rare | 95 | lowCollapseMultiplier | 0.18 | maxCollapse=30 | 붕괴 30 이하일 때 주문 피해 +18%. |
| 70032 | 여명 잉크병 | Epic | 120 | turnStartMp | 1 | - | 턴 시작 시 MP +1. |
| 70033 | 감전 증폭기 | Epic | 120 | statusSpecificMultiplier | 0.25 | statusId=shock | 감전 상태 적에게 주문 피해 +25%. |
| 70034 | 한기 절개도 | Epic | 120 | statusSpecificMultiplier | 0.25 | statusId=chill | 빙결 상태 적에게 주문 피해 +25%. |
| 70035 | 장기 보존제 | Epic | 120 | statusDurationBoost | 1 | - | 부여하는 상태 효과 지속 +1턴. |
| 70036 | 병렬 정령핵 | Heroic | 135 | elementalMastery | 0.25 | durationValue=1 | 속성 주입 족보 피해 +25%, 부여 상태 지속 +1턴. |
| 70037 | 봉인 사냥 장갑 | Heroic | 135 | bossDamageMultiplier | 0.3 | - | 보스와 최종 보스에게 주는 주문 피해 +30%. |
| 70038 | 상태 계통도 | Heroic | 135 | statusedEnemyMultiplier | 0.3 | - | 상태 이상이 걸린 적에게 주문 피해 +30%. |
| 70039 | 원소 총람 | Legendary | 145 | elementalMastery | 0.35 | durationValue=1, stackValue=1 | 속성 주입 족보 피해 +35%, 부여 상태 중첩 +1, 지속 +1턴. |
| 70040 | 종말의 주해 | Legendary | 145 | statusedEnemyMultiplier | 0.4 | - | 상태 이상이 걸린 적에게 주문 피해 +40%. |
| 70041 | 왕조 말소 인장 | Legendary | 145 | bossDamageMultiplier | 0.4 | - | 보스와 최종 보스에게 주는 주문 피해 +40%. |
| 70042 | 조율사의 나침반 | Rare | 95 | rerollDamageMultiplier | 0.08 | - | 남은 리롤 1회당 주문 피해 +8%. |
| 70043 | 백지 수호 도판 | Rare | 95 | fullHpMultiplier | 0.24 | - | 체력이 최대일 때 주문 피해 +24%. |
| 70044 | 서약의 버클 | Common | 70 | matchShield | 3 | - | 족보 일치 주문 발동 시 Shield +3. |
| 70045 | 도매상 영수증 | Epic | 120 | shopDiscount | 15 | - | 상점 유물 가격 -15G. |
| 70046 | 태양선 항법기 | Epic | 120 | rerollDamageMultiplier | 0.12 | - | 남은 리롤 1회당 주문 피해 +12%. |
| 70047 | 잔잔한 호수 양피지 | Epic | 120 | fullHpMultiplier | 0.32 | - | 체력이 최대일 때 주문 피해 +32%. |
| 70048 | 철합창 장정끈 | Rare | 95 | matchShield | 5 | - | 족보 일치 주문 발동 시 Shield +5. |
| 70049 | 붕괴 틈새 설계도 | Epic | 120 | lowCollapseMultiplier | 0.25 | maxCollapse=30 | 붕괴 30 이하일 때 주문 피해 +25%. |
| 70050 | 첫 불꽃 주석 | Common | 65 | firstCastDiscount | 1 | - | 매 턴 첫 주문의 MP 비용 -1. |
| 70051 | 오탈자 회수표 | Rare | 95 | refundOnMiss | 2 | - | 족보 불일치 시 MP 2 환급. |
| 70052 | 행렬 시계추 | Legendary | 145 | turnStartMp | 2 | - | 턴 시작 MP +2. |
| 70053 | 완충 제본끈 | Rare | 95 | turnStartShield | 5 | - | 턴 시작 Shield +5. |
| 70054 | 화염 분류표 | Rare | 95 | elementMultiplier | 0.35 | element=Fire | 화염 속성 주문 피해 +35%. |
| 70055 | 빙결 분류표 | Rare | 95 | elementMultiplier | 0.35 | element=Ice | 냉기 속성 주문 피해 +35%. |
| 70056 | 전격 분류표 | Rare | 95 | elementMultiplier | 0.35 | element=Electric | 전격 속성 주문 피해 +35%. |
| 70057 | 대지 분류표 | Rare | 95 | elementMultiplier | 0.35 | element=Ground | 대지 속성 주문 피해 +35%. |
| 70058 | 종막 눈금자 | Heroic | 135 | executeMultiplier | 0.32 | hpThreshold=0.5 | HP가 50% 이하인 적에게 주문 피해 +32%. |
| 70059 | 비단 가격 봉인 | Heroic | 135 | shopDiscount | 25 | - | 상점 유물 가격 -25G. |
| 70060 | 사냥 기입바늘 | Heroic | 135 | bossDamageMultiplier | 0.24 | - | 보스와 최종 보스에게 주는 주문 피해 +24%. |
| 70061 | 메아리 성벽 척추 | Heroic | 135 | matchShield | 8 | - | 족보 일치 주문 발동 시 Shield +8. |
