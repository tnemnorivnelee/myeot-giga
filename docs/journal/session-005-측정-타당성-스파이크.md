# Session 005 — 측정 타당성 스파이크 (NetworkStatsManager 실기기 검증)

**Date:** 2026-07-09

## 이번 세션에서 한 것

- **"지금 개발 시작이 맞는가"를 재검토** — session-004의 미결 2건(구현 순서·TDD 도구체인)은 ADR-006에서 이미 확정됐음을 확인. 남은 건 "첫 기능 착수"였음.
- **첫 착수를 순수 로직 TDD가 아니라 타당성 스파이크로 방향 전환** — 근거: ADR-006이 "측정을 먼저"라고 정한 이유는 측정이 최대 미검증 리스크이기 때문. 순수 산술(날짜·잔여량·임계값)을 먼저 TDD하는 건 리스크 없는 부분부터 손대며 진짜 리스크(ADR-002 "제조사 ROM 부정확 값 반환 우려")를 미루는 함정. 리스크는 test-first가 아니라 spike로 공략.
- **throwaway 스파이크 작성 → 실기기 검증**
  - Manifest에 `PACKAGE_USAGE_STATS` 선언
  - `MainActivity`를 스파이크로 교체: AppOps로 사용정보접근 권한 체크 → `Settings.ACTION_USAGE_ACCESS_SETTINGS` 유도 → `querySummaryForDevice(TYPE_MOBILE, null, 당월1일, now)` 호출 → rx/tx/합계 표시
  - 대상 기기: **SM-S911N (Galaxy S23, Android 16 / API 36)**
- **검증 결과 ✅** — 스파이크의 당월 모바일 총 사용량이 **기기 기본 설정 앱의 데이터 사용량과 정확히 일치**. 측정 전략(ADR-002) 유효 확정.
- **빌드 환경 이슈 발견·우회** — CLI `./gradlew`가 VS Code Red Hat Java 확장의 번들 **JRE(jlink 없음)**를 잡아 `JdkImageTransform` 실패. `JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home`로 우회해 `installDebug` 성공. (`compileDebugKotlin`만 돌 땐 이 변환이 안 걸려 통과했던 것.)

## 현재 상태

```
✅ ADR-002 측정 전략 — 실기기 검증 완료 (Galaxy S23/API 36에서 값 일치)
✅ 스파이크 코드 — spike/network-stats-feasibility 브랜치에 커밋 보존 (참조용)
✅ main — 깨끗 (스파이크는 main에 없음)
⏳ phase 1 본 구현 — 미착수
❗ CLI gradle 빌드 시 JAVA_HOME=Zulu 17 (또는 jlink 포함 JDK) 필요 — 안 하면 빌드 깨짐
```

## 다음 세션 시작할 것

**첫 행동: phase 1 본 구현 착수 — 청구 주기 날짜 계산의 실패 테스트부터.**

1. **Truth 의존성 추가** — `app/build.gradle.kts`의 `testImplementation`에 Truth (ADR-006이 "지금 추가"로 지정한 유일한 도구). 라이브러리 버전 카탈로그(`gradle/libs.versions.toml`)에 항목 추가 후 참조.
2. **첫 TDD 사이클 = 청구 주기 시작일 계산** (순수 Kotlin, Android 의존 0)
   - 스펙: 리셋 기준일(day-of-month, 예: 15)과 오늘 날짜 → 이번 주기 시작일 반환. 오늘 ≥ 기준일이면 이번 달, < 기준일이면 지난 달. 기준일이 31인데 그 달이 30일까지면 말일로 클램프(2월이면 28/29).
   - `app/src/test/java/...`에 실패 테스트 먼저 → 통과시키는 최소 구현 → 리팩터.
3. 이어서 잔여량/사용률 계산, 임계값 판정 (ADR-006이 지정한 나머지 순수 로직 2개).
4. `NetworkStatsManager` 호출은 `DataUsageReader` 인터페이스 뒤로 분리 — 순수 로직은 수동 Fake로 테스트, wrapper는 실기기 검증. **권한체크·쿼리 패턴은 `spike/network-stats-feasibility` 브랜치의 MainActivity 참조** (재작성 후 스파이크 브랜치는 삭제 가능).

### 주의 (환경)
- CLI에서 gradle 빌드/설치 시 `export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home` 먼저. 안 하면 jlink 없는 JRE를 잡아 `JdkImageTransform` 실패. (Android Studio에서 Run하면 자체 JBR을 써서 무관.)
- 실기기 검증은 `adb`에 기기 연결 후 `./gradlew :app:installDebug` → `adb shell monkey -p com.myeotgiga -c android.intent.category.LAUNCHER 1`.
