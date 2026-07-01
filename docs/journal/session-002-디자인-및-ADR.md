# Session 002 — 디자인 확보 + 기술 설계(ADR) 시작

**Date:** 2026-07-01

## 이번 세션에서 한 것

- session-001의 `git push` 완료
- 실기기로 앱 첫 실행 확인 ("Hello Android" 표시)
- 프로젝트 사이클 순서 재정렬: 문제정의 → PRD → **디자인** → 기술설계 → 개발계획 → 개발 → QA → 배포
  - 기능 개발로 바로 뛰어들지 않고, 디자인 → 기술 설계 순서로 진행하기로 함
- **디자인 프롬프트 작성**
  - `docs/design/prompts/claude-design-prompt.md`
  - `docs/design/prompts/stitch-prompt.md`
- **Google Stitch로 4개 화면 디자인 확보**
  - 다운로드한 원본 구조(`pretendard_1~4`, `care_clarity/`)를 의미 있는 구조로 리빌딩
  - `DESIGN.md`(Care & Clarity 디자인 시스템)는 CLAUDE.md와 같은 레이어인 프로젝트 루트로 이동
  - `docs/design/screens/01-dashboard/`, `02-settings/`, `03-widget-a/`, `04-widget-b/` 로 정리
- **ADR-002 작성**: NetworkStatsManager로 셀룰러 데이터 측정
  - 대안 TrafficStats 검토 → 재부팅 시 카운터 초기화되는 치명적 한계로 기각
- **ADR-003 작성**: WorkManager로 백그라운드 감시
  - AlarmManager와 비교: "15분 정확히 지킬 필요 없음" 확인 후에도, 재부팅/업데이트 시 알람 소멸·Doze 대응·버전별 분기 문제로 WorkManager가 우위
  - 주기는 배터리 절약을 위해 30분으로 설정

## 작업 방식 관련 피드백 (중요)

- **ADR/문서는 혼자 완성해서 제출하지 않기** — 섹션별로 제안하고 사용자와 함께 검수하며 작성 ([memory: feedback_collaborative_adr](../../CLAUDE.md))
- **커밋은 문서 하나당 하나씩 분리** — 별개 문서를 한 커밋에 묶지 않기 ([memory: feedback_one_commit_per_doc])
- 두 피드백 모두 `~/.claude/projects/.../memory/`에 영구 기록됨

## 현재 상태

```
✅ 실기기 첫 실행 확인 완료
✅ 디자인 4개 화면 확보 + docs/design/ 구조 정리 + 커밋
✅ ADR-002 (NetworkStatsManager) 작성 + 커밋
✅ ADR-003 (WorkManager) 작성 + 커밋
✅ GitHub push 완료 (origin/main = 12fafa3)
⏳ ADR-004, ADR-005 아직 미작성
```

## 다음 세션 시작할 것

1. **ADR-004 작성**: 홈 화면 위젯 구현 방식 (Glance API vs 클래식 AppWidgetProvider)
   - 사용자와 함께 후보 비교부터 시작 — 바로 결정 제시하지 말 것
2. **ADR-005 작성**: 설정/임계값 저장 방식 (DataStore vs SharedPreferences)
3. ADR-002~005 모두 완료되면 → 개발 계획(마일스톤/백로그) 단계로 이동
