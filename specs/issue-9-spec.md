# Technical Specification — Issue #9

> ⚠️ **Status note:** Issue #9 is **CLOSED** and the fix is **already merged**
> into the current codebase (commit `e2c9a61`, PR #10). `src/components/Footer.jsx`
> already renders the Contact Us tooltip (lines 190–205). This spec is therefore a
> **retrospective / verification document**, not a pending work item.

## 1. Issue Overview

| Field       | Value                                                                 |
| ----------- | --------------------------------------------------------------------- |
| Title       | Inside the footer, when user hovers on "Contact us" no text is displayed |
| Description | The footer "Contact Us" link had no hover help text, unlike the other footer links. Users need a short tooltip explaining the link navigates to the contact page where they can message the admin. |
| Labels      | (none)                                                                |
| Milestone   | (none)                                                                |
| State       | CLOSED (already resolved)                                             |
| Priority    | Low                                                                   |

## 2. Problem Analysis

Verified root cause from repository code:

- The footer policy links (`Privacy Policy`, `Terms of Service`, `Cookie Policy`)
  each render a `<div role="tooltip">` shown on hover via the `group` /
  `group-hover:*` Tailwind pattern.
- The `Contact Us` link (`<Link to="/contact">`) originally had the `group`
  wrapper and the gradient hover background, but was **missing the `tooltip` div**,
  so hovering produced no help text.

This is a purely presentational gap — a single missing markup block — not an
architectural defect. Complexity: **LOW**.

## 3. Proposed Solution (as implemented)

Minimal, pattern-matching change: add a `role="tooltip"` `div` inside the existing
`Contact Us` `<Link>`, reusing the exact classes, positioning, animation, and
arrow `<span>` already used by the other three footer links. No new components,
utilities, or state required.

Trade-off: copies the tooltip markup a fourth time rather than extracting a shared
`FooterLink`/`Tooltip` component. Acceptable for the smallest viable fix; an
optional future refactor is noted in §10.

## 4. Step-by-Step Implementation (completed)

1. **Locate the link** — `Contact Us` `<Link to="/contact">` in `Footer.jsx`.
2. **Insert tooltip div** — add the `role="tooltip"` block matching the sibling
   links' classes (`pointer-events-none absolute bottom-full ... group-hover:visible`).
3. **Author help text** — "Have a question or experiencing an issue? Send a message
   to our admin team and we'll get back to you as soon as possible." (apostrophe
   escaped as `&apos;` for JSX/lint compliance).
4. **Add arrow span** — the `border-t-gray-800` triangle, identical to siblings.

## 5. Verification Strategy

### Unit Tests

- N/A — project has no test runner configured; presentational-only change.

### Integration Tests

- N/A — no testing harness present in repo.

### Manual Checks

- Hover "Contact Us" in the footer → tooltip with help text appears above the link → ✅ (markup present).
- Click "Contact Us" → navigates to `/contact` → ✅ (`<Link to="/contact">`).
- Tooltip styling/animation matches Privacy/Terms/Cookie links → ✅ (identical classes).
- `npm run lint` passes (apostrophe escaped) → recommended re-check.
- Verify in both light/dark theme that contrast remains readable.

## 6. Files to Modify

| File Path                      | Nature of Change                                  |
| ------------------------------ | ------------------------------------------------- |
| `src/components/Footer.jsx`    | (Done) Added hover tooltip div to Contact Us link |

## 7. New Files to Create

| File Path | Purpose       |
| --------- | ------------- |
| (none)    | No new files. |

## 8. Existing Utilities to Leverage

| Utility                                   | Benefit                                            |
| ----------------------------------------- | -------------------------------------------------- |
| Tailwind `group` / `group-hover:*` pattern | Hover tooltip with no JS state, matching siblings. |
| Existing footer tooltip markup            | Copy-paste consistency for styling/animation/arrow. |
| `react-router-dom` `<Link>`               | Client-side nav to `/contact` already in place.    |

## 9. Acceptance Criteria

- [x] Hovering "Contact Us" displays help text.
- [x] Tooltip visually consistent with other footer links.
- [x] Link still navigates to `/contact`.
- [ ] Tests added — N/A (no test infrastructure).
- [x] No regressions to existing footer links.

## 10. Out of Scope

- Building or restyling the `/contact` page itself.
- Adding a test framework to the project.
- Optional refactor: extracting a shared `FooterLink` + `Tooltip` component to
  remove the 4× duplicated tooltip markup (quality improvement, not required by
  this issue).
- Accessibility enhancements beyond the existing pattern (e.g. keyboard-focus
  tooltip trigger, `aria-describedby`).
