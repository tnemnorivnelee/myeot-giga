# ADR-002: NetworkStatsManager로 셀룰러 데이터 사용량 읽기

- **Status**: Accepted
- **Date**: 2026-06-30

## Context

PRD에서 정의한 핵심 측정 요구사항:
- Wi-Fi 제외, 순수 모바일 데이터만 측정
- 당월 리셋 날짜 00:00:00 ~ 현재까지 누적 사용량
- 기기 전체 사용량 + Top 3 앱별 사용량

## 검토한 대안

### TrafficStats

Android 기본 제공 API로 구현이 단순하고 특수 권한이 불필요하다.

| 항목 | NetworkStatsManager | TrafficStats |
|------|---------------------|--------------|
| 시간 범위 필터 | ✅ 리셋 날짜~현재 지정 가능 | ❌ 없음 |
| Wi-Fi/모바일 분리 | ✅ | ✅ |
| 앱별 사용량 | ✅ | ✅ |
| 특수 권한 | 필요 | 불필요 |
| 재부팅 시 카운터 | 영향 없음 | **0으로 초기화** |

TrafficStats의 치명적 한계: **재부팅 시 카운터가 초기화**되어 "리셋 날짜 이후 누적 사용량" 계산이 불가능하다. 재부팅 시점 값을 저장해 누적 계산하는 우회 방법도 있으나, 오차가 발생하고 구현 복잡도가 오히려 NetworkStatsManager보다 높아진다.

→ **우리 요구사항(월간 리셋 날짜 기반 집계)에서 TrafficStats는 실질적으로 사용 불가.**

## Decision

**`NetworkStatsManager`** (API 23+)를 사용한다.

### 사용 방법

| 목적 | API |
|------|-----|
| 기기 전체 월간 사용량 | `querySummaryForDevice(TYPE_MOBILE, startTime, endTime)` |
| 앱별 사용량 (Top 3) | `queryDetails(TYPE_MOBILE, subscriberId, startTime, endTime)` |

- `startTime`: 당월 리셋 날짜 00:00:00 (사용자 설정)
- `endTime`: `System.currentTimeMillis()`
- `TYPE_MOBILE`: Wi-Fi 트래픽 자동 제외

### 필요 권한

| 권한 | 용도 | 부여 방식 |
|------|------|----------|
| `READ_NETWORK_USAGE_HISTORY` | 기기 전체 통계 | Manifest 선언만으로 부여 |
| `PACKAGE_USAGE_STATS` | 앱별 통계 | 사용자가 설정 > 특별 앱 권한에서 직접 허용 |
| `READ_PHONE_STATE` | SIM subscriberId 조회 | 런타임 퍼미션 요청 |

`PACKAGE_USAGE_STATS`는 일반 런타임 퍼미션이 아니므로, 앱 최초 실행 시 해당 설정 화면으로 안내하는 온보딩 흐름이 필요하다.

### subscriberId와 듀얼심

`queryDetails()`는 특정 SIM의 트래픽만 필터링하기 위해 `subscriberId`를 사용한다.  
듀얼심 기기에서 알뜰폰 회선의 트래픽만 정확히 집계하려면 해당 SIM의 `subscriberId`를 `TelephonyManager`로 조회해 전달해야 한다.

## Consequences

**Good**
- 서버 없이 기기 내에서 완결 — 네트워크 비용 0, 개인정보 외부 전송 없음
- API 23부터 지원 → minSdk 23과 일치, 별도 하위 호환 처리 불필요
- Wi-Fi/모바일 분리가 API 수준에서 보장됨

**Bad / 주의**
- `PACKAGE_USAGE_STATS` 권한은 사용자가 직접 설정 화면에서 허용해야 함 — 온보딩 UX 필요
- 제조사 커스텀 ROM에서 간헐적으로 통계 초기화 또는 부정확한 값을 반환하는 사례가 보고됨 (검증 필요)
- Android 10+ 에서 `subscriberId` 조회에 `READ_PHONE_STATE` 외 추가 제약이 생겼으므로, 구현 시점에 API 레벨별 분기 확인 필요
