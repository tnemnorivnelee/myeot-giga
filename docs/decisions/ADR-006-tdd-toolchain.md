# ADR-006: 구현 순서 및 TDD 도구체인 — JUnit 4 + 수동 Fake + Truth

- **Status**: Accepted
- **Date**: 2026-07-05

## Context

session-004에서 TDD 방식으로 개발을 진행하기로 했고, PRD 3.1~3.4 구현 순서는 후보만 제시된 채 보류 상태였다. 이 ADR은 두 가지를 확정한다:

1. **구현 순서** — 4가지 후보 중 선택
2. **TDD 도구체인** — Jest(러너+assertion+mocking 일체형)와 달리 Kotlin/Android는 카테고리별로 별도 라이브러리를 선택해야 함

핵심 제약: 이 앱의 중심인 `NetworkStatsManager`는 Android 시스템 서비스라 **JVM 단위 테스트가 불가능**하다. 따라서 도구 선택 이전에 "무엇을 JVM에서 테스트하고 무엇을 실기기에서 검증할 것인가"의 구조 결정이 선행되어야 한다.

현재 설정된 테스트 도구 (프로젝트 생성 시 기본값):

- JUnit 4 (4.13.2) — 로컬 단위 테스트
- androidx JUnit + Espresso + Compose UI Test — 계측(기기) 테스트

## 의사결정 과정

### 1단계: 구현 순서 확정

session-004에서 제시된 4가지 후보:

| 후보 | 설명 |
|---|---|
| **측정 → 저장 → UI → 위젯 → 백그라운드** (기본안) | 데이터 흐름 순서대로, 의존성 방향과 일치 |
| 백그라운드 먼저 검증 | WorkManager 제약(Doze 등)이 최대 리스크라는 관점 |
| 설정 먼저 | 사용자 입력값이 모든 계산의 전제라는 관점 |
| 대시보드 먼저 | 눈에 보이는 결과물로 빠른 피드백을 얻는 관점 |

**기본안 채택.** 근거:

- `NetworkStatsManager` 측정이 이 앱의 핵심 리스크다 — 기기별 오차, 권한(사용 정보 접근) 문제가 여기서 판가름 난다. 측정이 안 되면 나머지는 의미가 없으므로 가장 먼저 검증한다.
- TDD 관점에서도 유리하다 — 측정 단계의 로직(청구 주기 날짜 계산, 잔여량 계산, 임계값 판정)은 Android 의존성 없는 순수 Kotlin으로 작성 가능해, 도구체인이 단순한 상태에서 TDD 사이클을 시작할 수 있다.
- 각 단계의 산출물이 다음 단계의 입력이 되므로(측정값 → 저장 → 표시), 통합 시점에 인터페이스 불일치가 발생할 여지가 적다.

### 2단계: 테스트 가능한 구조 확인

`NetworkStatsManager` 호출을 얇은 인터페이스(예: `DataUsageReader`) 뒤에 숨기고, 로직은 순수 Kotlin 클래스로 분리한다:

- **JVM 단위 테스트 (TDD 대상)** — 날짜 계산, 잔여량/사용률 계산, 임계값 판정 등 순수 로직. 의존성은 인터페이스의 수동 Fake로 대체.
- **실기기 검증** — `NetworkStatsManager`를 실제 호출하는 얇은 wrapper. 기기별 오차 확인이라는 phase 1의 목적상 어차피 실기기가 필수라, JVM 시뮬레이션의 실익이 없다.

이 구조가 이후 도구 선택을 좌우한다: Robolectric이 불필요해지고, mocking 수요가 최소화된다.

### 3단계: 카테고리별 후보 비교

**테스트 프레임워크 (러너)**

| 도구 | 장점 | 단점 |
|---|---|---|
| JUnit 4 | Android 공식 기본값, 이미 설정됨, 모든 도구·문서가 이걸 전제 | 중첩 구조 없음, parameterized 테스트 문법 불편 |
| JUnit 5 | `@Nested`, `@ParameterizedTest` 등 모던 API | Android 공식 지원 없음 — 서드파티 플러그인(android-junit5) 필요. Compose UI 테스트는 여전히 JUnit 4 Rule 기반이라 두 버전 공존 부담 |
| Kotest | Kotlin 네이티브, BDD 스타일, property-based 테스트 내장 | 학습 곡선, Android 통합 매끄럽지 않음, 커뮤니티 자료 적음 |

**Mocking**

| 도구 | 장점 | 단점 |
|---|---|---|
| 수동 Fake | 의존성 0, 디버깅 쉬움, 상태 기반 검증이라 리팩터링에 강함. Google 공식 테스트 가이드도 mock보다 fake 우선 권장 | 인터페이스가 많아지면 유지보수 부담, 호출 여부 검증은 수동 구현 필요 |
| MockK | Kotlin 사실상 표준, final 클래스·코루틴 지원 | relaxed mock 남용 시 실제 동작과 괴리, 에러 메시지 난해 |
| Mockito (+ mockito-kotlin) | Java 진영 표준, 자료 많음 | Kotlin final 클래스 추가 설정 필요, `when` 예약어 충돌 등 DSL 어색 — Kotlin 프로젝트에서 MockK 대신 고를 이유 없음 |

**Assertion**

| 도구 | 장점 | 단점 |
|---|---|---|
| JUnit 기본 | 이미 있음 | 실패 메시지 빈약 (`expected: 5 but was: 3` 수준) |
| Truth (Google) | Android 공식 문서 권장, 실패 메시지 상세 — TDD의 red 단계에서 원인 파악이 빨라짐 | 의존성 1개 추가 (가벼움) |
| Kotest Assertions | `shouldBe` DSL, 러너 없이 단독 사용 가능 | DSL 취향 갈림, infix 함수라 IDE 자동완성 덜 자연스러움 |

**Android 의존 코드**

| 도구 | 장점 | 단점 |
|---|---|---|
| Robolectric | JVM에서 실행 → 기기 없이 빠른 피드백 | `NetworkStatsManager`의 shadow가 부실해 이 프로젝트 핵심에 무용. 초기화 느림, 실기기와 동작 차이 |
| 계측 테스트 (이미 설정됨) | 실제 동작 그대로 검증 — 기기별 오차 확인 목적에 부합 | 느림, CI에서 에뮬레이터 필요 |

**코루틴/Flow (미래 대비)**

- kotlinx-coroutines-test — 사실상 유일한 선택지. 코루틴 도입 시점(저장/백그라운드 phase)에 추가.
- Turbine — Flow 전용 테스트 헬퍼. Flow 사용 시점에 검토.

### 4단계: 판단 기준

- **phase 1에 필요한 것만 지금 추가한다.** 측정 단계의 TDD 대상은 순수 Kotlin 로직이므로, 러너(JUnit 4)와 Fake만 있으면 사이클이 돈다. mocking 라이브러리와 코루틴 테스트는 필요해지는 phase에서 추가해도 늦지 않고, 그때 추가하는 비용도 지금과 동일하다.
- **Truth만 예외로 지금 추가한다.** 비용이 의존성 1줄로 거의 0인 반면, TDD는 실패 메시지를 계속 읽는 워크플로라 개선 체감이 크고, 나중에 추가하면 기존 assertion과 혼재하는 비용이 생긴다.
- 이미 설정된 것(JUnit 4, 계측 테스트)은 대체할 명확한 이유가 없는 한 유지한다.

## Decision

**구현 순서: 측정 → 저장 → UI → 위젯 → 백그라운드**

**TDD 도구체인:**

| 구분 | 도구 |
|---|---|
| 지금 사용 | JUnit 4 (유지) + 수동 Fake + **Truth (추가)** |
| 필요 시 추가 | MockK (Fake가 3개 이상으로 늘어 유지보수가 부담될 때), kotlinx-coroutines-test (코루틴 도입 시), Turbine (Flow 테스트 시) |
| 제외 | JUnit 5, Kotest, Mockito, Robolectric |

`NetworkStatsManager`는 얇은 인터페이스로 감싸 순수 로직과 분리하고, wrapper는 실기기로 검증한다.

## Consequences

**Good**

- 의존성 추가가 Truth 1개뿐 — 빌드 설정 변경 최소, 학습 부담 최소
- 순수 Kotlin 로직 중심의 빠른 JVM 테스트 → TDD 사이클(red-green-refactor)이 초 단위로 돈다
- Fake 기반 상태 검증은 리팩터링 시 테스트가 깨지지 않아 유지보수 비용이 낮음
- React/TS 경험과 매핑됨: 순수 함수 분리 후 테스트하는 패턴, `jest.mock` 대신 수동 test double 쓰는 패턴과 동일

**Bad**

- JUnit 4의 오래된 API — 중첩 구조 없이 평평한 테스트 클래스, parameterized 테스트가 불편. 로직 테스트가 수백 개 규모가 되면 재검토 필요
- "호출됐는가" 상호작용 검증이 필요한 테스트는 Fake에 수동으로 기록 코드를 넣어야 함 — 이 패턴이 반복되면 MockK 도입 신호
- 도구를 단계적으로 추가하는 방식이라, 코루틴 도입 등 전환 시점마다 도구체인 결정을 한 번씩 다시 해야 함 (이 ADR의 "필요 시 추가" 표가 그 기준)
