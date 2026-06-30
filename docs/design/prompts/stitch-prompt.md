# Google Stitch Prompt — 몇기가 (Myeot-Giga)

Stitch는 화면별로 프롬프트를 따로 입력합니다. 아래 각 섹션을 화면 하나씩 붙여넣으세요.

---

## 공통 컨텍스트 (매 프롬프트 앞에 붙이거나 Design System으로 설정)

App name: 몇기가
Platform: Android
Design system: Material Design 3
Audience: Elderly Korean users, non-tech-savvy
Style: Clean, minimal, high readability, calm color palette, no ads

---

## Screen 1: Main Dashboard

```
A clean Android home screen for a mobile data usage tracker app called "몇기가".

Material Design 3. Target users are elderly adults — prioritize large text and simple layout.

Layout (top to bottom):
- Top app bar with title "몇기가"
- Large circular arc progress gauge in the center showing monthly data usage. Label "이번 달 사용량" above it. Primary value "12.5 GB / 20 GB" inside the arc. Below the arc, smaller text "7.5 GB 남음" (remaining).
- A card below the gauge labeled "오늘 사용량" showing "0.8 GB"
- A card below labeled "데이터 많이 쓴 앱" with a list of 3 rows: each row shows a colored app icon placeholder, app name, and data used. Row 1: YouTube 4.2 GB, Row 2: Instagram 3.1 GB, Row 3: Chrome 1.8 GB
- Bottom navigation bar with two items: home icon (selected) and settings icon
```

---

## Screen 2: Settings

```
Android settings screen for a mobile data usage tracker app called "몇기가".

Material Design 3. Large, readable text for elderly users.

Layout (top to bottom):
- Top app bar with back arrow and title "설정"
- Section: "요금제 초기화 날짜" — a list item with title, subtitle "매월 데이터가 초기화되는 날짜", and trailing value "1일" with a chevron
- Section: "기본 데이터 용량" — a list item with title and trailing value "20 GB" with a chevron
- Section header: "알림 설정"
- Subtitle text: "사용량이 아래 수치에 도달하면 알림을 받습니다"
- Row of chips/tags: "10 GB ×", "15 GB ×", "18 GB ×" each with a delete button
- A filled tonal button "+ 추가" below the chips
```

---

## Screen 3: Home Widget — Option A (숫자형)

```
Android home screen widget for a mobile data usage tracker app called "몇기가".

Widget size: 4×2 grid cells. Material Design 3 widget style with rounded corners and a soft translucent or light background.

Content:
- Small app name "몇기가" in the top-left corner
- A horizontal linear progress bar showing roughly 62% filled
- Large bold text below the bar: "12.5 GB / 20 GB"
- Tiny text at the bottom: "15분 전 업데이트"
```

---

## Screen 4: Home Widget — Option B (퍼센트형)

```
Android home screen widget for a mobile data usage tracker app called "몇기가".

Widget size: 4×2 grid cells. Material Design 3 widget style with rounded corners and a soft translucent or light background.

Content:
- Small app name "몇기가" in the top-left corner
- A horizontal linear progress bar showing roughly 62% filled
- Large bold percentage text below the bar: "62.5%"
- Smaller text below: "12.5 GB 사용"
- Tiny text at the bottom: "15분 전 업데이트"
```
