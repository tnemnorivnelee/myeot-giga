# Session 001 — 프로젝트 초기 셋업

**Date:** 2026-06-30

## 이번 세션에서 한 것

- 프로젝트 이름 결정: `myeot-giga` (몇 기가?)
- GitHub 레포 생성 + 초기 커밋
- `docs/` 구조 생성: decisions / product / design / journal
- `README.md`, `docs/product/PRD.md`, `.gitignore` 작성
- ADR-001 작성: Kotlin + Jetpack Compose 선택 이유
  - 홈 화면 위젯이 핵심 기능 → RN 구조적 불가
  - 부모님이 주 사용자 → 위젯 없으면 앱의 존재 이유 없음
  - Jetpack Compose ↔ React 선언형 패턴 유사성
- Android Studio 설치 + 프로젝트 초기화 (Kotlin + Compose, API 23)
- `CLAUDE.md` 작성: LLM 행동 가이드라인 + 프로젝트 컨텍스트 + 세션 기반 문서화 사이클

## 현재 상태

```
✅ GitHub 레포: https://github.com/tnemnorivnelee/myeot-giga
✅ Android 프로젝트 초기화 완료
✅ ADR-001 커밋됨
⏳ GitHub push 아직 안 함 (로컬 커밋만 된 상태)
```

## 다음 세션 시작할 것

1. `git push` 로 GitHub에 올리기
2. Android Studio에서 에뮬레이터 또는 실기기로 앱 첫 실행 확인
3. 본격 구현 시작 — 첫 번째 기능 결정 필요
   - 후보: NetworkStatsManager로 데이터 사용량 읽기 / 홈 화면 위젯 기본 틀 / 메인 화면 UI
