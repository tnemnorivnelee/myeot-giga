# 몇기가 (myeot-giga)

> 광고 없는, 클린한 셀룰러 데이터 모니터링 앱

알뜰폰 eSIM 회선을 사용하는 부모님을 위해 만든 안드로이드 앱.
공식 앱이 없는 알뜰폰 사업자의 데이터 잔량을 직관적으로 확인하고, 원하는 시점에 알림을 받을 수 있다.

---

## 왜 만들었나

- 알뜰폰 사업자가 데이터 확인 앱을 제공하지 않음
- 기존 시장 앱들은 광고 과다 + 구식 UI로 부모님이 쓰기 불편
- 직접 만들어서 드리기로 결정

## 핵심 기능

- 당월 셀룰러 데이터 누적 사용량 실시간 확인
- 다중 임계값 알림 (예: 10GB, 15GB, 18GB 도달 시 각각 알림)
- 홈 화면 위젯 (게이지 + 숫자 or 퍼센트)
- 백그라운드 자동 갱신 (~15분 주기)
- 오늘 사용량 + 데이터 많이 쓴 앱 Top 3 대시보드

## 기술 스택

- **Language**: Kotlin
- **UI**: Jetpack Compose
- **Background**: WorkManager
- **Widget**: Jetpack Glance
- **Data**: NetworkStatsManager (Android System API)

## 문서

- [PRD](docs/product/PRD.md) — 제품 요구사항 정의서
- [ADR](docs/decisions/) — 기술 의사결정 기록
- [기술 설계](docs/design/) — 아키텍처 설계 문서
- [개발 일지](docs/journal/) — 구현 과정 기록

## 플랫폼

Android 전용. iOS는 시스템 API 정책상 셀룰러 데이터 전체 조회 불가.
