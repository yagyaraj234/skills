---
name: frontend-readiness
description: Check frontend readiness from a working diff, PR URL, release target, or full repository audit. Use for app, UI, accessibility, performance, and frontend PR reviews.
---

# Frontend readiness

Invoke as `$frontend-readiness diff`, `$frontend-readiness pr <URL>`, `$frontend-readiness release`, or `$frontend-readiness full`.

Use the mode, evidence, verdict, and edit rules in [check-readiness](../check-readiness/SKILL.md). When invoked directly, select a mode there. When invoked by `check-readiness`, keep its selected target. Assess frontend surfaces only.

Assess checks that apply to the chosen target. Mark an in-scope exception `N/A` with a reason. A section that does not apply (non-public app for Search and sharing, no framework for Framework conventions) is one `N/A` line, not one per check.

Blocking areas: broken critical flow or route, auth and permission UI, unsafe rendering of user content, leaked secrets, accessibility failures on critical flows, missing error or empty states on data-driven surfaces, failing build or tests. Other failures are advisory.

### Correctness

- [ ] Every in-scope route renders through direct navigation, refresh, in-app navigation, back, and forward.
- [ ] Every critical flow completes its happy path and preserves useful input on recoverable failure.
- [ ] Forms validate input, prevent duplicate submission, show actionable errors, and expose errors to assistive technology.
- [ ] Client, server, URL, and persisted state remain consistent across refresh, cancellation, stale responses, and repeated actions.
- [ ] Console has no new errors or warnings; failed network requests are explained.
- [ ] Auth and permission boundaries show correct UI, preserve intended return routes, and handle expired sessions, forbidden responses, and role changes.

Every data-driven surface has an explicit state matrix. Mark a state `N/A` only when it cannot occur or offers no user-facing value.

- [ ] **Empty:** successful zero-data result explains what happened and offers a useful next action.
- [ ] **Loading:** progress is clear, useful existing content stays stable, and repeated actions stay controlled.
- [ ] **Skeleton:** where final layout is predictable and delay is perceptible, placeholders match it and avoid layout shift; otherwise record `N/A`.
- [ ] **Partial:** available content stays usable while independent sections load or fail.
- [ ] **Error:** message is actionable, useful input is preserved, and retry or recovery works.

### Framework conventions

Skip items for frameworks the project does not use; name the detected framework and version.

- [ ] Framework, router, and data-loading APIs match the installed version and existing project conventions; record the version and source of truth.
- [ ] Internal navigation uses the detected framework link component, such as `Link` from `next/link` or `@tanstack/react-router`; native `<a>` handles external, download, email, and phone links.
- [ ] Route params, search params, redirects, and navigation use the framework router APIs and hooks.
- [ ] Static and dynamic styles follow the existing framework-supported styling system; dynamic values use CSS custom properties unless library-required positioning needs inline styles.

### Interface and accessibility

- [ ] Layout works at each recorded viewport without unintended overflow. Long text, missing media, slow data, and large collections remain usable.
- [ ] Hierarchy, spacing, typography, colors, and interactive feedback are consistent; controls cover hover, focus, active, disabled, loading, and error states.
- [ ] Destructive actions require clear intent and offer recovery where practical.
- [ ] Semantic landmarks, headings, controls, labels, and accessible names match purpose.
- [ ] Keyboard-only use reaches and operates every interactive element in logical order; focus stays visible and moves correctly through dialogs, menus, errors, and route changes.
- [ ] Text and essential UI meet the recorded accessibility target (WCAG 2.2 AA unless a stricter requirement applies); meaning does not depend on color alone.
- [ ] Images have appropriate alternatives; decorative media stays silent. Zoom, text resizing, reduced motion, and required screen-reader announcements preserve the flow.

### Resilience, privacy, and security

- [ ] User-controlled content is rendered safely; secrets and privileged decisions remain server-side.
- [ ] External links, redirects, uploads, storage, and browser APIs validate untrusted input. External tabs use safe opener behavior where applicable.
- [ ] Offline and timeout behavior avoids data loss.
- [ ] Error UI omits sensitive internals while logs retain enough diagnostic context.
- [ ] Authentication or personal-data products expose public Terms and Privacy pages from auth and persistent navigation; content matches actual behavior.
- [ ] Cookies, CSRF defenses, response security headers, redirects, and cache policy are verified when the application controls them.
- [ ] Analytics, error tracking, and consent respect privacy requirements and do not expose personal or secret data.
- [ ] Feature flags have a verified target-environment value and, for risky changes, a documented rollback or disable path.

### Performance and assets

- [ ] Representative routes are measured from a production build in the recorded environment using TTFB, LCP, INP, CLS, first useful content, and primary-interaction timing.
- [ ] Every in-scope route shows useful content or an actionable fallback and accepts its primary interaction within the recorded budget; five seconds is the default only when no stricter product budget exists.
- [ ] Production build output, route chunks, and network waterfall contain no unexplained large cost.
- [ ] Changed performance optimizations have measured benefit and preserve keyboard, focus, screen-reader, and offline behavior where applicable.
- [ ] Effects release listeners, subscriptions, observers, pending requests, workers, timers, animation frames, and debounced or throttled work when dependencies change or components unmount.
- [ ] Images use available framework optimization, responsive dimensions and `sizes`, modern formats, compression, stable aspect ratios, and correct eager/lazy priority. Video and fonts have deliberate delivery, fallback, and preload behavior.
- [ ] Third-party, inline, and blocking scripts have an explicit purpose and loading priority.

### Search and sharing

Applies only to public or shareable routes; authenticated-only apps need just the non-indexing check.

- [ ] Every route defines appropriate title and description metadata. Public indexable routes set canonical and robots intent through framework APIs; non-production and protected environments are not accidentally indexed.
- [ ] Shareable routes resolve Open Graph and social metadata to absolute, crawlable values. OG images return the correct content type, dimensions, and alt text; rendered tags and a real share preview are verified.
- [ ] For a release gate, Lighthouse runs against deployed production routes on recorded mobile and desktop profiles; Performance, Accessibility, Best Practices, and SEO results and regressions are recorded.

### Engineering and deployment

- [ ] Formatter, lint, typecheck, tests, and production build pass or have an explicit baseline failure.
- [ ] Changed logic has the smallest useful automated regression check.
- [ ] Environment variables, feature flags, API URLs, browser support, and test accounts match the target environment.
- [ ] Error tracking, favicons, and install icons work where the product requires them.
- [ ] For a release gate or deployment-affecting change, deployed assets, routes, APIs, auth, headers, redirects, and cache behavior are verified in the target environment.

