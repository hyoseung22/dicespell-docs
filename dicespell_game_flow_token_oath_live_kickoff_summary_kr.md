# DiceSpell `Run Table` Oath Banner live kickoff summary

## 3줄 요약
- 이 문서는 battle green 뒤 oath 첫 실전 턴을 **어떤 순서로 열어야 하는지** 빠르게 읽는 companion이다.
- 핵심은 oath를 `ending 전체 polish`로 열지 않고, `oath-banner` 한 stage로만 여는 것이다.
- 첫 10분은 code edit보다 먼저 `archive / manifest / locator / non-change line`을 잠그는 시간이 돼야 한다.

**한 줄 목표:** oath 첫 live turn을 `archive 생성 -> manifest/stub 초안 -> banner locator 재확인 -> oath-only edit -> evidence drop` 5단계 kickoff로 고정한다.

---

## 이 문서는 누구를 위한가
- **프로그래머:** oath에서 지금 닫아야 할 범위가 정확히 어디까지인지 빨리 확인할 사람
- **UI:** `banner > result > carry` hierarchy가 first-glance로 읽히는지만 빠르게 보고 싶은 사람
- **아트:** `Oath Banner Starter` sanity만 확인하고 큰 ending 리뉴얼은 미루려는 사람
- **QA / PM:** candidate / green / queue closing 문장을 kickoff와 섞지 않으려는 사람

---

## 왜 지금 필요한가
이미 oath에는 stage packet, required artifacts, candidate review 문서가 있다.

그래도 실제 turn 직전에는 이 질문이 남았다.

> `좋아, 이제 oath가 unlock됐어. 그런데 첫 10분에 정확히 무엇부터 해야 하지?`

이 요약은 그 공백만 닫는다.

---

## first screen 결론
- 지금 열 수 있는 것은 **oath-banner 하나뿐**이다.
- 첫 편집 전에는 반드시 `stage archive + manifest 초안 + stub 첫 줄 + banner semantic locator + 금지 범위 선언`이 있어야 한다.
- 이 다섯 개가 없으면 아직 kickoff도 끝난 게 아니다.
- `ending helper`, `reward/shop side family`, `queue closing`, `Run Table complete` 문장은 이번 턴 범위 밖이다.

---

## oath 첫 10분 kickoff

| 구간 | 지금 해야 하는 일 | 완료 기준 |
| --- | --- | --- |
| T-10 ~ T-8 | `docs/artifacts/run-table-token-delivery/oath-banner/` archive와 starter file 6종 생성 | 폴더와 파일이 실제로 존재하고 `oath green` 문장은 아직 없음 |
| T-8 ~ T-6 | `manifest.md`와 stub 첫 줄을 oath-only 문장으로 채움 | 빈 파일 / generic placeholder 없음 |
| T-6 ~ T-4 | oath root, banner/result/carry role, CTA context locator 재확인 | locator note에 `line drift가 있어도 stage는 넓히지 않음` 메모가 있음 |
| T-4 ~ T-2 | 허용 범위와 금지 범위를 다시 freeze | manifest와 handoff note가 같은 boundary를 말함 |
| T-2 ~ T+10 | oath-only edit 후 required artifacts evidence 초안 drop | archive 안에 overview/focus/note/check가 canonical naming으로 생김 |

---

## 프로그래머가 먼저 가져갈 결론
- oath 첫 turn은 `renderEnding(summary)` 전체가 아니라 **oath banner family**만 닫는 턴이다.
- 지금 필요한 건 helper 대수술보다 `data-run-surface="overrun-oath"`, `data-oath-role="banner|result|carry"`, `data-overrun-cta`를 review 가능한 hook으로 남기는 일이다.
- battle 재개방, queue closing hook 추가, ending helper 구조 재설계는 이번 stage에서 금지다.

---

## UI가 먼저 가져갈 결론
- 캡처는 두 장이면 충분하다.
  - `oath-1440-overview.png`
  - `oath-1440-banner-focus.png`
- overview는 **main banner가 먼저 읽히는가**, focus는 **banner / result / carry가 한 장에서 읽히는가**만 증명하면 된다.
- side card나 CTA가 더 먼저 튀면 이번 stage는 fail note가 맞다.

---

## 아트가 먼저 가져갈 결론
- 이번 턴 아트 일은 `Oath Banner Starter` sanity note 하나면 충분하다.
- ending 배경 전체 리뉴얼, 공용 side shell, reward/shop 공용 frame은 지금 열 일이 아니다.
- starter note에는 `이번 stage에서 아직 안 하는 것`을 같이 적어야 한다.

---

## QA / PM이 먼저 가져갈 결론
- kickoff 메모는 candidate도 green도 아니다.
- `oath archive-ready candidate`는 required artifacts 8종이 같은 oath stage 언어를 쓸 때만 가능하다.
- `oath-banner green`과 `Run Table token queue closing review-only unlock`은 QA candidate 뒤에만 쓴다.
- `Overrun Oath 거의 완료`, `ending 사실상 green`, `Run Table 완료` 같은 큰 문장은 전부 금지다.

---

## 금지 패턴
- `이 김에 ending helper도 같이`
- `reward/shop side family도 이번 pass에`
- `queue closing 문구도 미리`
- `Overrun Oath 전반 polish`
- archive 없이 임시 캡처 / 임시 메모만 남기기

---

## immediate next actions
1. oath를 열 때는 이 문서로 먼저 archive/stub/locator/freeze를 잠근다.
2. 그다음 `oath stage packet -> required artifacts -> candidate review` 순서로 넘어간다.
3. 실제 evidence가 생기기 전에는 `queue closing review-only unlock`이나 총괄 progress 문장을 쓰지 않는다.

---

## 리스크 & 후속과제
- 가장 큰 리스크는 oath 문서가 충분해졌다는 이유로 banner stage를 다시 `ending 전체 polish`로 읽는 것이다.
- 다음 oath 실제 turn은 `archive 먼저`, `locator note 먼저`, `overview/focus 질문 먼저`를 지켜야 한다.
- 후속 문서는 oath도 battle처럼 semantic anchor / archive file map / filled archive examples / handoff scorecard ladder를 끝까지 갖추는 쪽이 우선이다.
