# ADR-005: 설정/임계값 저장 방식 — DataStore (Preferences) 선택

- **Status**: Accepted
- **Date**: 2026-07-01

## Context

PRD 3.1 설정 요구사항: 요금제 리셋 날짜(단일값), 기본 데이터 용량(단일값), 다중 알림 임계값(추가/삭제 가능한 목록).

후보: **SharedPreferences** vs **Jetpack DataStore (Preferences DataStore)**

## 의사결정 과정

### 1단계: 디자인 확정본 기준 데이터 형태 확인

`docs/design/screens/02-settings` 확인 결과:

- 요금제 초기화 날짜: 단일 값
- 기본 데이터 용량: 단일 값
- 임계값 목록: 칩 형태로 추가/삭제만 가능, 드래그로 순서 바꾸는 UI 없음, 표시도 오름차순 정렬

→ 저장 순서를 보존할 필요가 없고 중복도 의미 없는 값(같은 GB를 두 번 추가할 이유 없음) — **순서 없는 Set**이면 충분하다.

이 발견으로 "임계값 목록은 순서 있는 리스트라 별도 직렬화가 필요하다"는 초기 가정을 정정한다. SharedPreferences의 `StringSet`, DataStore의 `stringSetPreferencesKey` 모두 Set을 기본 타입으로 직접 지원 — 데이터 모델링 관점에서는 두 후보가 동급이며 우회가 필요 없다.

### 2단계: 후보별 장단점

**SharedPreferences**

| 장점 | 단점 |
|---|---|
| Android 기본 제공 — 별도 의존성 추가 불필요 | 동기 API — `getInt()`/`getString()` 등이 즉시 값을 반환하지만, 내부적으로 XML 파일을 메모리로 읽어들이는 과정이 있어 첫 접근 시 메인 스레드에서 디스크 I/O가 발생할 수 있음 |
| 학습 자료 압도적으로 많음, 검증된 방식 | 변경 관찰에 `registerOnSharedPreferenceChangeListener` 콜백 등록이 필요하고, 해제하지 않으면 메모리 누수 위험 — Compose에서 쓰려면 `DisposableEffect`로 등록/해제를 직접 관리하는 보일러플레이트 필요 |
| 초기 설정 코드 최소 (인스턴스 얻어서 바로 사용) | ADR-003의 `CoroutineWorker.doWork()`(suspend 함수) 안에서 동기 API를 호출하는 건 가능하지만, 코루틴의 이점(논블로킹, 취소 대응)을 살리지 못함 |
| | Google 공식 문서가 신규 프로젝트에는 DataStore 사용을 권장 |

**DataStore (Preferences DataStore)**

| 장점 | 단점 |
|---|---|
| 모든 읽기가 `Flow<Preferences>`로 노출 — `CoroutineWorker.doWork()`에서는 `.first()`로 한 번 읽고, Compose에서는 `.collectAsState()`로 계속 구독하는 식으로 동일 API를 양쪽에 그대로 사용 | 의존성 추가 필요 (`androidx.datastore:datastore-preferences`) |
| `edit { }` 블록으로 트랜잭션 쓰기 — 여러 값을 원자적으로 갱신, 쓰는 도중 프로세스가 죽어도 일부만 반영되는 문제 없음 | 초기 설정 코드가 SharedPreferences보다 몇 줄 더 필요 (`by preferencesDataStore(name = "settings")` 델리게이트 선언) |
| 리스너 등록/해제 보일러플레이트 없음 — Flow 구독 자체가 리스너 역할 | 학습 자료가 SharedPreferences보다 적음. 다만 이 프로젝트는 React/TS 배경이라 `Flow`가 Promise/RxJS Observable과 개념적으로 유사해 진입장벽은 낮은 편 |
| Google이 SharedPreferences의 공식 후속으로 설계, 장기 지원 방향 | |

### 3단계: 판단 기준

데이터 모델링 자체는 두 후보가 동급이라 격차가 없다. 남는 차이는 "코드베이스 전반에서 반복되는 비용이냐, 한 번만 지불하는 비용이냐"다.

- SharedPreferences의 단점(리스너 보일러플레이트, 메인 스레드 I/O 위험)은 설정을 읽는 모든 지점(WorkManager 워커, 대시보드 Compose UI)마다 반복해서 떠안는 비용이다.
- DataStore의 단점(의존성 추가, 초기 설정 몇 줄, 학습 곡선)은 프로젝트 초기에 한 번만 지불하면 끝나는 비용이다.

이미 ADR-003에서 WorkManager(코루틴 기반)를 채택했고, 대시보드도 Compose 반응형 UI로 설계되므로, 반복 비용을 없애는 DataStore 쪽이 장기적으로 유리하다.

## Decision

**Jetpack DataStore (Preferences DataStore)**를 채택한다. 임계값은 `stringSetPreferencesKey`로 저장하고, 표시 시점에 오름차순으로 정렬한다.

## Consequences

**Good**
- WorkManager 워커, Compose UI 양쪽 모두 `Flow`로 리스너 보일러플레이트 없이 연동
- `edit { }` 트랜잭션으로 다중 값 갱신 시 데이터 일관성 보장
- 메인 스레드 블로킹 위험 없음
- Google 공식 권장 방향, 장기 지원 예상

**Bad**
- SharedPreferences 대비 초기 설정(의존성 추가, DataStore 인스턴스 생성 보일러플레이트)이 약간 더 필요
- Proto DataStore 대비 타입 안정성은 낮음(문자열 키 기반) — 현재 요구사항(단일값 2개 + Set 1개) 수준에서는 충분
- `Flow`/코루틴이 처음이라면 초반 학습 곡선 존재 (단, Promise/RxJS 경험이 있으면 개념 자체는 유사)
