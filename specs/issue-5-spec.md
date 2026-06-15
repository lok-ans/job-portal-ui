# Technical Specification — Issue #5

> ⚠️ **Status note:** Issue #5 is **CLOSED** and the fix is **already merged**
> into the current codebase (commit `ff0f250`, PR #6). `src/components/Footer.jsx`
> already renders the Privacy Policy tooltip (lines 144–158). This spec is therefore
> a **retrospective / verification document**, not a pending work item.

## 1. Issue Overview

| Field       | Value                                                                      |
| ----------- | -------------------------------------------------------------------------- |
| Title       | In the footer, when we hover on to "Privacy Policy" nothing is being displayed |
| Description | The footer "Privacy Policy" link showed no hover text; it should display explanatory text about the privacy policy on hover. |
| Labels      | (none)                                                                     |
| Milestone   | (none)                                                                     |
| Assignee    | lok-ans                                                                    |
| State       | CLOSED (already resolved)                                                  |
| Priority    | Low                                                                        |

## 2. Problem Analysis

Verified root cause from repository code:

- The footer renders a row of small policy links. The intended UX is a hover
  tooltip (Tailwind `group` / `group-hover:*` pattern) showing descriptive text.
- The `Privacy Policy` link had the `group` wrapper but **no `<div role="tooltip">`**
  child, so hovering produced no visible text.

This was the first instance of a recurring footer-tooltip gap; the same defect was
later reported for Cookie Policy / Terms and for Contact Us ([[issue-9-spec]]).
Purely presentational — a single missing markup block. Complexity: **LOW**.

## 3. Proposed Solution (as implemented)

Minimal change: add a `role="tooltip"` `div` inside the existing `Privacy Policy`
link, using the `pointer-events-none absolute bottom-full ... group-hover:visible`
classes plus the `border-t-gray-800` arrow span. No new components, utilities, or
state. This established the reusable tooltip pattern that the other footer links
(Terms, Cookie, Contact) now follow.

Trade-off: tooltip markup is duplicated per link rather than extracted into a shared
component — acceptable for the smallest viable fix (see §10).

## 4. Step-by-Step Implementation (completed)

1. **Locate the link** — `Privacy Policy` `<a>` in `Footer.jsx`.
2. **Insert tooltip div** — add the `role="tooltip"` block with the standard
   positioning/animation classes.
3. **Author help text** — privacy-policy summary describing data collection, no
   third-party selling, and user control over their data.
4. **Add arrow span** — the `border-t-gray-800` triangle pointing to the link.

## 5. Verification Strategy

### Unit Tests

- N/A — project has no test runner configured; presentational-only change.

### Integration Tests

- N/A — no testing harness present in repo.

### Manual Checks

- Hover "Privacy Policy" in the footer → tooltip with descriptive text appears → ✅ (markup present, lines 144–158).
- Tooltip styling/animation/arrow render correctly above the link → ✅.
- Pattern reused consistently by sibling links (Terms, Cookie, Contact) → ✅.
- `npm run lint` passes → recommended re-check.
- Verify readability/contrast in both light and dark theme.

## 6. Files to Modify

| File Path                   | Nature of Change                                     |
| --------------------------- | ---------------------------------------------------- |
| `src/components/Footer.jsx` | (Done) Added hover tooltip div to Privacy Policy link |

## 7. New Files to Create

| File Path | Purpose       |
| --------- | ------------- |
| (none)    | No new files. |

## 8. Existing Utilities to Leverage

| Utility                                    | Benefit                                              |
| ------------------------------------------ | ---------------------------------------------------- |
| Tailwind `group` / `group-hover:*` pattern | Hover tooltip with no JS state.                      |
| Footer tooltip markup (established here)    | Became the template reused by Terms/Cookie/Contact.  |

## 9. Acceptance Criteria

- [x] Hovering "Privacy Policy" displays descriptive text.
- [x] Tooltip visually consistent with the footer design.
- [ ] Tests added — N/A (no test infrastructure).
- [x] No regressions to other footer links.

## 10. Out of Scope

- Building or restyling a dedicated Privacy Policy page.
- Adding a test framework to the project.
- Optional refactor: extract a shared `FooterLink` + `Tooltip` component to remove
  the now 4× duplicated tooltip markup across footer links.
- Accessibility enhancements beyond the existing hover pattern (keyboard-focus
  trigger, `aria-describedby`).
