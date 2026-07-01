# ADR-004: 홈 화면 위젯 구현 방식 — Glance API 선택

- **Status**: Accepted
- **Date**: 2026-07-01

## Context

PRD 3.3 요구사항: 홈 화면 위젯 2종(Option A: 숫자형, Option B: 퍼센트형)으로 셀룰러 데이터 사용량 표시.

후보: **Glance API** vs **클래식 AppWidgetProvider (RemoteViews)**

### 비교 대상을 이 두 가지로 한정한 이유

홈 화면 위젯은 우리 앱이 아니라 런처(다른 앱, 다른 프로세스)가 그려주는 화면이다. 프로세스 경계를 넘어야 하므로 일반 View나 Compose 트리를 그대로 넘길 수 없고, Android가 강제하는 유일한 전달 방식이 **RemoteViews**(제한된 View 목록만 담아 직렬화·역직렬화 가능한 뷰 명세)다.

Glance API도 내부적으로는 결국 RemoteViews를 생성해 전달한다 — 즉 Glance와 클래식 AppWidgetProvider는 **같은 렌더링 엔진(RemoteViews) 위에서 개발자가 코드를 작성하는 두 가지 방법**일 뿐, 서로 다른 렌더링 기술이 아니다. 이 때문에 "제3의 방식"은 OS 차원에서 존재하지 않는다.

(참고: 알림·Quick Settings 타일 등 다른 표시 화면도 논의했으나, 알림은 PRD 3.2의 기존 1회성 알림으로 이미 커버되고 Quick Settings 타일은 이번 스코프에서 제외하기로 확정 — 별도 결정 불필요.)

## 의사결정 과정

### 1단계: 디자인 확정본 레이아웃 복잡도 검토

`docs/design/screens/03-widget-a`, `04-widget-b` 확인 결과, 두 위젯 모두 구성 요소가 단순함:

- Row (신호 아이콘 + 앱 이름 텍스트)
- 진행바 (선형 progress indicator)
- Column (큰 숫자 텍스트 + 보조 텍스트)
- Footer 텍스트 (마지막 업데이트 시각)

리스트, 스크롤, 커스텀 드로잉 없음 → Glance 기본 컴포저블(`Row`/`Column`/`Text`/`Image`/`LinearProgressIndicator`)만으로 전부 표현 가능.

### 2단계: 두 작성 방식 비교

| | Glance API | AppWidgetProvider (RemoteViews 직접 작성) |
|--|--|--|
| 문법 | Compose 스타일 선언형 Kotlin | XML 레이아웃 + 명령형 RemoteViews API |
| 이 프로젝트와의 정합성 | 이미 Compose 사용 중(`app/build.gradle.kts`) → 학습 곡선 낮음 | 별도 패러다임 추가 학습 필요 |
| 문서/커뮤니티 자료 | 상대적으로 적음 | 압도적으로 많음, 검증된 방식 |
| 복잡한 레이아웃(리스트 등) 지원 | 제한적 | 강력 |
| 이 프로젝트 위젯 레이아웃 요구사항 | 충분히 커버됨 | 불필요하게 과함 |
| minSdk 호환 (`minSdk = 23`) | 호환 | 호환 |

레이아웃이 단순해 RemoteViews 직접 작성 시 얻는 확장 기능(리스트, 커스텀 드로잉)이 필요한 지점이 없음 → 클래식 방식의 자료량 우위가 실익으로 이어지지 않음. Compose와 문법이 통일되는 이점만 순수하게 남음.

## Decision

**Glance API** (`androidx.glance:glance-appwidget`)를 채택한다.

## Consequences

**Good**
- Compose와 동일한 선언형 문법 → 학습 곡선 낮음, 코드베이스 일관성 유지
- Google 공식 권장 방향, 장기 지원 예상
- XML 레이아웃 파일 불필요, 보일러플레이트 적음

**Bad**
- 신생 라이브러리라 스택오버플로/블로그 등 커뮤니티 자료가 RemoteViews 대비 적음 → 트러블슈팅 시 공식 문서 의존도가 높아짐
- 향후 위젯에 리스트나 복잡한 커스텀 UI가 필요해지면 Glance 표현력의 한계에 부딪힐 수 있음 (현재 확정 디자인 기준으로는 해당 없음)
