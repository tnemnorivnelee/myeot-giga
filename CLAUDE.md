# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. Project Context

**Developer:** React/TypeScript background. Kotlin and Android are new territory.
**App:** Mobile data usage widget + notifications for MVNO (알뜰폰) users. Targets family members unfamiliar with smartphones.
**Stack:** Kotlin + Jetpack Compose, min API 23, `NetworkStatsManager` for data measurement.

When suggesting approaches, factor in that idiomatic Android patterns may be unfamiliar. Explain the "why" behind Kotlin/Android-specific decisions, not just the "what".

## 6. Documentation Cycle

This project treats documentation as part of the development process — the same cycle used in a real company.

Context windows are small. A single task may span multiple chat sessions. Treat each session as a handoff.

**Starting a session:**
- Read the latest `docs/journal/session-NNN-*.md` to understand where the previous session ended.
- Check `git log --oneline -5` to confirm the current state.

**Ending a session:**
- Write `docs/journal/session-NNN-[topic].md`. The most critical section is "Next session starts here" — the exact next action, not a vague direction.

**Before implementing a significant technical decision:**
- Write an ADR in `docs/decisions/` first.

**On release:**
- Update `CHANGELOG.md`.

Journal entries are not optional. If the next session can't pick up without asking clarifying questions, the journal failed.

## 7. Low Coupling, High Cohesion

**Each unit has one reason to change. Depend on interfaces, not internals.**

- If a function/class mixes unrelated responsibilities, split it.
- Don't reach into another module's internal state — go through its public interface.
- If fixing one thing breaks something unrelated, that's a coupling problem. Fix the boundary, not the symptom.

The test: Could this module change without forcing changes in unrelated files? If not, the boundary is wrong.

## 8. Git 브랜치 전략

**모든 기능/버그 작업은 브랜치에서. `main`은 완료된 작업만.**

- 브랜치명: `feature/이슈번호-기능명` (예: `feature/12-widget-percent-mode`), 버그 수정은 `fix/이슈번호-설명`
- 이슈 번호가 없는 문서/설정 작업은 `docs/설명`, `chore/설명`처럼 성격만 표기
- 작업 시작 전 `main`에서 새 브랜치를 만든다
- 작업 완료 후 PR을 열고, 병합 전 diff를 셀프 리뷰한다
- 강제(브랜치 보호)는 걸지 않는다 — 습관으로 지키는 컨벤션

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.