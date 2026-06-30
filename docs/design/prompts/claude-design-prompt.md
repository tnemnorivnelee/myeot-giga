# Claude Design Prompt — 몇기가 (Myeot-Giga)

아래 프롬프트를 Claude Design에 그대로 붙여넣어 사용하세요.

---

## Prompt

Design a clean Android mobile app called **"몇기가"** (meaning "How many GB?").

**App purpose:** Helps elderly parents who use budget MVNO eSIM plans track their monthly mobile data usage. They are not tech-savvy, so the UI must be extremely simple and readable at a glance. No ads, no clutter — only what matters.

**Design direction:**
- Material Design 3 (Android)
- Clean, minimal, trustworthy
- Large, legible text — the primary users are older adults
- Calm color palette (suggest something that feels light and easy on the eyes)
- No promotional banners, no decorative noise

---

Please design the following 4 screens:

---

### Screen 1: Main Dashboard (메인 화면)

The home screen of the app. Shows the user's current monthly data usage status.

**Components:**
- App bar with title "몇기가"
- **Monthly usage section** (most prominent element):
  - Circular or arc progress gauge showing used vs. total data
  - Label: "이번 달 사용량"
  - Value display: e.g., `12.5 GB / 20 GB`
  - Remaining data in smaller text: e.g., `7.5 GB 남음`
- **Today's usage section:**
  - Label: "오늘 사용량"
  - Value: e.g., `0.8 GB`
- **Top 3 apps section:**
  - Label: "데이터 많이 쓴 앱"
  - List of 3 rows, each showing: app icon placeholder, app name, data used (e.g., YouTube 4.2 GB / Instagram 3.1 GB / Chrome 1.8 GB)
- Bottom navigation or a settings icon to navigate to settings

---

### Screen 2: Settings (설정 화면)

Where the user configures their plan information and notification thresholds.

**Components:**
- App bar with title "설정"
- **Plan reset date setting:**
  - Label: "요금제 초기화 날짜"
  - Description: "매월 데이터가 초기화되는 날짜"
  - Selector showing a day number (e.g., `1일`)
- **Total data capacity setting:**
  - Label: "기본 데이터 용량"
  - Input showing GB value (e.g., `20 GB`)
- **Notification thresholds section:**
  - Label: "알림 설정"
  - Description: "사용량이 아래 수치에 도달하면 알림을 받습니다"
  - List of threshold chips/tags (e.g., `10 GB`, `15 GB`, `18 GB`) with a delete (×) button on each
  - An "+ 추가" button to add a new threshold
- Save or auto-save indicator

---

### Screen 3: Home Widget — Option A (홈 화면 위젯 A)

A small Android home screen widget (approximately 2×1 or 4×1 grid size).

**Shows:**
- App name or small icon
- Arc/linear progress bar
- Numeric display: `12.5 GB / 20 GB`
- Last updated time in tiny text (e.g., `15분 전 업데이트`)

Design it to look like a native Android widget — rounded corners, translucent or solid background.

---

### Screen 4: Home Widget — Option B (홈 화면 위젯 B)

Same widget as Option A but shows percentage instead of raw numbers.

**Shows:**
- App name or small icon
- Arc/linear progress bar
- Percentage display: `62.5%`
- Subtext: `12.5 GB 사용`
- Last updated time in tiny text

---

**Additional notes:**
- The app should feel trustworthy and calm — not flashy or gamified
- Prioritize readability over visual complexity
- The gauge/progress element should be the visual centerpiece of the main screen and widget
- Use realistic sample data in all mockups (as shown in the component descriptions above)
