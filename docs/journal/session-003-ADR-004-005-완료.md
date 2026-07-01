# Session 003 — ADR-004, ADR-005 완료

**Date:** 2026-07-01

## 이번 세션에서 한 것

- **ADR-004 작성**: 홈 화면 위젯 구현 방식 — Glance API 선택
  - 클래식 AppWidgetProvider(RemoteViews)와 비교
  - 위젯 디자인 확정본(`03-widget-a`, `04-widget-b`) 확인 → 레이아웃이 단순해 Glance 기본 컴포저블로 충분함을 확인
  - "왜 이 두 방식만 후보인가" 짚음 — 홈 화면 위젯은 런처(다른 프로세스)가 그리므로 RemoteViews 전달이 OS 차원의 유일한 방식, Glance도 결국 RemoteViews를 생성하는 API일 뿐
  - 중간에 알림 상시 표시 + Quick Settings 타일 추가 여부 논의 → 알림은 기존 PRD 1회성 알림으로 이미 커버 확인, QS 타일은 스코프 제외로 확정
- **ADR-005 작성**: 설정/임계값 저장 방식 — DataStore(Preferences) 선택
  - SharedPreferences와 비교
  - 설정 화면 디자인(`02-settings`) 확인 → 임계값 목록은 순서 없는 Set이면 충분하다는 걸 발견, "리스트라 직렬화 필요"라는 초기 가정 정정
  - 후보별 장단점을 표로 세분화, "반복 비용 vs 1회성 비용" 기준으로 판단 근거 명시
  - 코루틴 개념 설명 (JS/TS `async`/`await`, `Flow`는 RxJS Observable에 대응)
- **origin/main으로 push 완료** (`fbc598e..a8d0140`)

## 현재 상태

```
✅ ADR-002 (NetworkStatsManager) — Accepted
✅ ADR-003 (WorkManager) — Accepted
✅ ADR-004 (Glance API) — Accepted
✅ ADR-005 (DataStore) — Accepted
✅ GitHub push 완료 (origin/main = a8d0140)
⏳ 개발 계획(마일스톤/백로그) 단계 미시작
```

## 다음 세션 시작할 것

1. **개발 계획(마일스톤/백로그) 단계 진입** — ADR-002~005가 모두 끝났으므로 다음 단계
2. PRD 3.1~3.4 기능을 어떤 순서로 구현할지 **사용자와 함께 논의하며 정하기** (바로 순서를 제시하지 말고 후보안부터 비교하는 이번 세션 방식 유지)
3. 논의 시작점 후보: 측정 로직(ADR-002) → 저장(ADR-005) → 대시보드 UI → 위젯(ADR-004) → 백그라운드 알림(ADR-003) 순서가 자연스러워 보이나, 확정은 다음 세션에서
