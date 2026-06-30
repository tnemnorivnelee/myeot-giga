# ADR-001: 네이티브 Android (Kotlin + Jetpack Compose) 선택

- **Status**: Accepted
- **Date**: 2026-06-30

## Context

알뜰폰 데이터 사용량을 가족 구성원이 쉽게 확인할 수 있는 Android 앱을 만든다.
핵심 기능 요구사항:

1. **홈 화면 위젯** — 현재 데이터 사용량을 잠금 해제 없이 확인
2. **임계값 도달 시 푸시 알림** — 백그라운드에서 지속 감지
3. **데이터 사용량 측정** — Android `NetworkStatsManager` API (API 23+)로 기기 내 측정

개발자는 웹 프론트엔드(React / TypeScript) 경력자로, 크로스플랫폼 프레임워크(React Native)도 검토했다.

## Decision

**Kotlin + Jetpack Compose (순수 네이티브 Android)**를 선택한다.

## Rationale

### 결정적 이유: 홈 화면 위젯

이 앱의 주 사용자는 **부모님**이다. 스마트폰 사용이 익숙하지 않은 분들이 데이터 잔여량을 확인하려면 앱을 찾아서 열고, 로그인하고, 화면을 탐색해야 한다 — 이 과정 자체가 장벽이다. 홈 화면 위젯은 이 장벽을 없앤다. 잠금 해제만 하면 바로 보인다.

위젯이 없으면 앱의 존재 이유가 없다.

Android 홈 화면 위젯은 `AppWidget API`를 통해 구현되며, 앱과 **완전히 분리된 별도 프로세스**에서 `RemoteViews`로만 렌더링된다. React Native의 JavaScript 런타임은 이 프로세스에 접근할 수 없어 위젯 구현이 구조적으로 불가능하다.

### 추가 근거

| 요소 | React Native | Kotlin + Compose |
|------|-------------|-----------------|
| 홈 화면 위젯 | ❌ 불가 | ✅ AppWidget API |
| 백그라운드 작업 | 제한적 | ✅ WorkManager |
| 시스템 API 접근 | 브릿지 필요 | ✅ 직접 접근 |
| 학습 목표 (네이티브) | 낮음 | ✅ 높음 |

## Consequences

- Kotlin / Android SDK를 새로 학습해야 한다
- iOS 지원은 불가 (처음부터 Android only 범위)
- Jetpack Compose는 선언형 UI로 React 경험과 개념적으로 유사하여 적응 비용이 낮다
