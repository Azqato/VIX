# Product Requirements Document

**Product:** VIX Strategy
**Version:** 1.2.1
**Status:** Live. MVP shipped, in active development.
**Last Updated:** 2026-08-25
**Repository:** https://github.com/Azqato/vix
**Live site:** https://azqato.github.io/vix/

---

## About This Document

This is the complete written record of VIX Strategy. As of v1.2.1 the project keeps
exactly three documents plus a root README, and this file absorbed seven others that
previously stood alone: `TRD.md`, `ROADMAP.md`, `METRICS.md`, `TENETS.md`, `RUNBOOK.md`,
`SECURITY.md`, and `PRFAQ.md`. Their content is here, in the sections named after them.
The mapping from old file to new section is in
[Deprecation and Removal](#deprecation-and-removal).

It is written so that a new contributor, human or model, can understand the entire
project without reading any code. Sections repeat context deliberately rather than
cross-referencing, because a reader may arrive at any one of them directly.

Prose here follows the project [writing style](#writing-style). No em dashes.

**Contents**

- [Problem Statement](#problem-statement)
- [Target Users](#target-users)
- [Goals](#goals)
- [Non-Goals](#non-goals)
- [User Stories](#user-stories)
- [Feature List](#feature-list)
- [Strategy Logic](#strategy-logic)
- [Constraints](#constraints)
- [Assumptions](#assumptions)
- [Success Criteria](#success-criteria)
- [Tenets](#tenets)
- [Roadmap](#roadmap)
- [Metrics](#metrics)
- [Runbook](#runbook)
- [Technical Requirements](#technical-requirements)
- [Conventions](#conventions)
- [Writing Style](#writing-style)
- [Browser Testing](#browser-testing)
- [Security](#security)
- [Deprecation and Removal](#deprecation-and-removal)
- [Documentation Versus Reality](#documentation-versus-reality)
- [Risks and Open Questions](#risks-and-open-questions)
- [Working Practice](#working-practice)
- [Press Release](#press-release)
- [Frequently Asked Questions](#frequently-asked-questions)

---

## Problem Statement

Retail investors using a buy-and-hold strategy have no systematic mechanism to respond to
market volatility signals. When the CBOE VIX spikes, historically a leading indicator of
imminent recovery, most investors panic-sell at exactly the wrong moment rather than
increasing exposure. There is no free, simple, browser-based tool that computes
rules-based ETF allocations from live VIX data without requiring a brokerage account, a
Python environment, or a financial advisor.

The gap is narrower than "investing tools are hard." Plenty of sites publish the VIX
number, and plenty of sites publish opinions about what to do when it moves. Almost none
turn the number directly into a specific, reproducible set of percentages, and the ones
that do sit behind a signup wall, a subscription, or a spreadsheet the user has to build
themselves. The friction between "I can see the VIX is 38" and "therefore my portfolio
should be 10 percent BIL, 30 percent SPY, 40 percent QQQ, 20 percent TQQQ" is where the
discipline breaks down, and that friction is exactly what this product removes.

---

## Target Users

**Primary: Aggressive long-term retail investor.**
Self-directed, with a 10-year-plus time horizon, comfortable with leveraged ETFs and
drawdowns of 50 to 95 percent. Actively monitors market conditions but wants a
rules-based system rather than discretionary decisions. Uses a Roth IRA or a taxable
brokerage account. Already familiar with TQQQ, QQQ, and SPY, so no glossary or
onboarding flow is needed. This user's core need is not information, it is a
precommitment device: something that tells them what the rule says at the moment their
instincts are least reliable.

**Secondary: Rules-based investing researcher.**
Interested in systematic strategies and backtesting frameworks. Uses this app as a live
reference while developing their own allocation models. Wants to see the full tier table
at once, not just the currently active row, and wants the arithmetic to be visible and
checkable. May share or cite the tool in investment communities, which makes the tier
reference table on the dashboard a distribution feature as much as a usability one.

**Tertiary: Developer or curious observer.**
Arrives via GitHub, wants to understand how the strategy works technically, and may fork
or adapt the codebase. This user is served by the deliberate absence of a build step: the
repository is the deployable artifact, so reading it and running it are the same act.

---

## Goals

1. Present the VIX-based allocation strategy as a credible, visually compelling investing
   framework.
2. Display real-time VIX-driven portfolio allocations without any backend or server.
3. Serve as a versioned, evolving public tool with a transparent changelog.

---

## Non-Goals

- Not a brokerage or trading platform. No order execution, no account integration.
- Not a backtesting engine. No historical return simulation in the current version.
- Not a general financial planning tool. One strategy, one fixed set of risk categories.
- Not a personalized advisor. Does not account for individual tax situation, risk
  tolerance, or existing holdings.
- Not a native mobile app. Web only, no iOS or Android wrapper.
- Will not store any user data on any server.
- Will not display advertising or collect analytics without explicit user consent.
- Not a real-time quote service. The VIX value shown is a periodically refreshed
  snapshot, not a streaming tick. See [Documentation Versus Reality](#documentation-versus-reality)
  entry D-01 for how far this currently diverges from the word "live" used in the UI.

---

## User Stories

- As a retail investor, I want to see the current VIX value and which allocation tier it
  maps to, so that I know what my portfolio should look like right now.
- As a retail investor, I want the allocation percentages to update automatically as the
  VIX changes, so that I do not have to do the math myself.
- As a researcher, I want to see all five VIX tiers and their allocations in one table,
  so that I can understand the full strategy at a glance.
- As a long-term investor, I want a clear risk disclosure on every page, so that I
  understand the dangers of leveraged ETFs before using this strategy.
- As a developer, I want clean, modular JavaScript with no build system, so that I can
  read and fork the codebase easily.
- As any visitor, I want the last-known VIX value to display instantly on load even
  before the network request completes, so that I never see a blank state.
- As any visitor, when no cached value is available and the app is fetching data, I want
  to see an animated loading counter (`Loading 1`, `Loading 2`, and so on) so that I know
  the app is working and not frozen.
- As any visitor, if the VIX data is completely unavailable and there is no cached value,
  I want to see a clear error message (`Error Please Refresh`) instead of a silent blank,
  so that I know to retry.
- As any visitor, I want the site to work even if the VIX data source is temporarily
  unreachable, so that I still see the most recent allocation.
- As a retail investor who prefers different funds than the defaults, I want to substitute
  my own ticker into each risk category while keeping the same VIX-tier logic, so that I
  can apply the strategy's mechanics to funds I already hold or trust.
- As a visitor who opened the files from disk rather than a web server, I want the site
  to work identically, so that I can inspect or run it without any tooling.

---

## Feature List

### MVP (shipped, v1.0.x)

- **Pitch page (`index.html`).** Hero with live VIX gauge and tier badge, problem
  statement (three cards), VIX mean-reversion insight section with three historical
  stats and a Buffett quote, the full five-tier allocation table, an AI and
  semiconductor thesis section, a risk disclosure section, and a footer call to action.
- **Strategy dashboard (`strategy.html`).** Large live VIX number with a status badge
  and an Eastern Time timestamp, an active tier banner, a Chart.js donut chart with the
  VIX value drawn in the center, an allocation breakdown table, a full tier reference
  table with the active tier highlighted, and a strategy summary accordion.
- **VIX data.** Primary source is `data/vix.js`, refreshed on a schedule by a GitHub
  Actions workflow that fetches Yahoo Finance server-side (added v1.1.0). Falls back to
  a direct browser fetch via the allorigins.win CORS proxy if the data file is
  unavailable. No backend at request time.
- **localStorage caching.** The VIX value persists across tabs and browser sessions with
  a 30-minute TTL, and is read synchronously on page load.
- **Status badges.** LIVE, CACHED, STALE, ERROR, and LOADING states with distinct
  colors.
- **Loading counter.** When a fetch is required (no cache, or a cache older than 30
  minutes), the VIX number area shows `Loading 1`, `Loading 2`, and so on, incrementing
  every second until the response arrives. On total failure with no cache it changes to
  `Error Please Refresh` in red.
- **Auto-refresh.** `strategy.html` runs `refresh()` on a 60-second interval. Note that
  the 30-minute cache TTL short-circuits most of those calls, so the displayed value
  changes at most every 30 minutes. See [Documentation Versus Reality](#documentation-versus-reality)
  entry D-02.
- **Manual refresh.** A button that clears `localStorage['vix_last_known']` and re-runs
  the fetch path. See entry D-03 for what this actually re-reads.
- **Sticky navigation.** About, Dashboard, Custom, Azqato Invests, and Support links with
  an active-page green highlight and `aria-current="page"`.
- **Footer credit.** "Built by Azqato" with a link, on all pages.
- **Responsive design.** Functional from 375px wide upward, with breakpoints at 900px,
  768px, and 480px.

### Shipped post-MVP

- **Server-side VIX data pipeline (v1.1.0).** A scheduled GitHub Actions workflow fetches
  the VIX and commits it to `data/vix.js`. All scripts converted from ES modules to
  classic scripts so the site works under the `file://` protocol.
- **Inline boot script scope fix (v1.1.1).** Wrapped each page's inline boot script in an
  IIFE to stop a global redeclaration `SyntaxError` introduced by the v1.1.0 conversion.
- **Custom strategy builder (`custom.html`, v1.2.0).** Free-text ticker entry per risk
  category (Risk Off, Diversify, Risk On, Full Risk), reusing the core strategy's
  VIX-tier percentage weights unchanged. Has its own VIX feed, tier banner, chart, and
  allocation table. Selections persist in `localStorage` only. Ticker input is sanitized
  against a character allowlist but is not verified against a live quote.
- **Documentation consolidation (v1.2.1).** Ten documents reduced to four. No code
  change. This document is the result.

### Future (post-launch)

Ordered as of the 2026-07-09 reprioritization, which put data-visualization work ahead of
strategy variants and ticker verification.

| Feature | Target version |
|---------|----------------|
| 12-month historical VIX sparkline | v1.3.0 (previously slated as v1.2.1, renumbered because v1.2.1 was used by this audit) |
| VIX percentile rank mode | v2.0.0 |
| Trend filter, QQQ against its 200-day moving average | v2.1.0 |
| SMH and SOXL "Growth Rocket" strategy as a toggle | v2.2.0 |
| Live ticker verification for the Custom builder | v2.3.0 |
| Backtesting visualization using Yahoo Finance historical data | v3.0.0 |
| Full portfolio tracker with user-entered holdings | v4.0.0 |

Per-milestone detail is in the [Roadmap](#roadmap).

---

## Strategy Logic

### VIX Allocation Tiers

| VIX Range | BIL | SPY | QQQ | TQQQ | Fear Level |
|-----------|-----|-----|-----|------|-----------|
| Below 15 | 25% | 50% | 20% | 5% | Low (Complacency) |
| 15 to 25 | 20% | 40% | 30% | 10% | Moderate |
| 25 to 35 | 15% | 35% | 35% | 15% | Elevated |
| 35 to 45 | 10% | 30% | 40% | 20% | High |
| 45 and above | 5% | 20% | 50% | 25% | Extreme (Crisis) |

Boundaries are evaluated with strict less-than comparisons in `getTier()`, so a VIX of
exactly 15.00 lands in the second tier, exactly 25.00 in the third, and so on. A value of
exactly 45.00 or higher lands in tier 5, which is the catch-all.

All values are exact and each row already sums to 100. A normalization step
(`normalize()` in `assets/js/strategy.js`) divides each weight by the row total and
rounds to four decimal places, which currently changes nothing. It is retained as a guard
so that an edited raw table that does not sum to 100 still renders a valid allocation.

**Tickers:**

- **BIL**, SPDR Bloomberg 1-3 Month T-Bill ETF. Near-cash stability. Minimizes drawdown
  risk during calm markets.
- **SPY**, SPDR S&P 500 ETF. Broad US market exposure. The core equity holding across all
  tiers, and the only position that is never eliminated.
- **QQQ**, Invesco Nasdaq-100 ETF. Tech and growth concentration. Rises with fear.
- **TQQQ**, ProShares UltraPro QQQ 3x Leveraged ETF. Maximum recovery capture at peak
  fear. Never exceeds 25 percent of the portfolio at any tier, which is the single most
  important risk control in the design.

### Rebalancing Rules

- Monthly, on the first trading day of each month.
- Immediately when the VIX crosses into a different tier, in either direction.

These rules are stated in the UI on `index.html` and `strategy.html` and are not
implemented in code. The product tells the user what the target allocation is; the user
executes in their own brokerage account.

### Custom Category Mapping

`custom.html` does not introduce a second strategy. It renames the four positions and
lets the user pick the instrument for each, while the tier boundaries and the percentage
weights stay byte-identical to the table above.

| Category (shown to user) | Internal slot | Default ticker | Meaning |
|--------------------------|---------------|----------------|---------|
| Risk Off | `BIL` | BIL | Capital preservation |
| Diversify | `SPY` | SPY | Broad, diversified core |
| Risk On | `QQQ` | QQQ | Growth-oriented exposure |
| Full Risk | `TQQQ` | TQQQ | Maximum recovery capture |

The internal slot names are plumbing and are never shown in the UI.

### Future Strategy Version: VIX-Timed Growth Rocket (post-MVP, not implemented)

| VIX Range | QQQ | SMH | TQQQ | SOXL |
|-----------|-----|-----|------|------|
| Below 15 | 35% | 35% | 20% | 10% |
| 15 to 20 | 30% | 30% | 25% | 15% |
| 20 to 30 | 20% | 25% | 30% | 25% |
| 30 to 40 | 10% | 15% | 35% | 40% |
| Above 40 | 5% | 10% | 35% | 50% |

Additional tickers: **SMH** (VanEck Semiconductor ETF) and **SOXL** (Direxion Daily
Semiconductor Bull 3x ETF). Note that this variant uses different tier boundaries than
the core strategy (15/20/30/40 rather than 15/25/35/45), which means shipping it is not
purely a table swap; `getTier()` would need to become tier-set-aware. That is not
currently reflected in the v2.2.0 milestone scope and is logged as open question 6.

---

## Constraints

- **Hosting.** GitHub Pages, static files only, no server-side execution at request time.
- **No build tooling.** No Node.js, no bundler, no npm, no `package.json`. The repository
  must deploy as raw files.
- **CORS.** The Yahoo Finance API blocks direct browser requests. The primary data path
  avoids this entirely by fetching server-side in GitHub Actions and committing the
  result. The fallback path, used only if the committed data is unavailable, must go
  through a public CORS proxy.
- **`file://` compatibility.** The site must work when the HTML files are opened directly
  from disk. This rules out ES modules, `fetch()` of local files, and anything else the
  `file://` origin blocks. It is the reason for the entire classic-script architecture.
- **Legal.** A risk disclaimer must be displayed on every page that mentions TQQQ.
- **No data collection.** Zero analytics, zero cookies, zero user tracking in the current
  version.
- **Single maintainer.** There is no team, no review rotation, and no on-call. Anything
  that requires ongoing operational attention is effectively out of scope.

---

## Assumptions

These are accepted as true without full information. Each one is a place the product
could break.

- The allorigins.win CORS proxy remains publicly available at no cost. Fallback path
  only, as of v1.1.0.
- GitHub Actions scheduled workflows remain enabled. GitHub auto-disables schedules in
  repositories with no commit activity for 60 days, so the project depends on either
  regular commits or the data-update commits themselves resetting that clock. See open
  question 1: as of this audit, `data/vix.js` has not changed since 2026-07-09.
- Yahoo Finance's internal JSON API at `/v8/finance/chart/` continues to return VIX data
  under `chart.result[0].meta.regularMarketPrice`. It is an undocumented, unsupported
  endpoint and can change without notice.
- jsDelivr continues to serve Chart.js 4.4.0 at the pinned URL.
- Users are already familiar with ETF investing concepts, so no onboarding flow is
  required.
- VIX values remain within roughly 0 to 100. The tier5 catch-all handles any value at or
  above 45, so there is no upper-bound failure, but the UI's fixed-width number display
  assumes at most four characters plus a decimal point.
- TQQQ and QQQ remain publicly available ETFs with sufficient liquidity.
- The user executes rebalances themselves. The product has no way to verify this and no
  feedback loop that would tell us whether anyone actually does.

---

## Success Criteria

| Criterion | How it is checked | Status at 2026-08-25 |
|-----------|-------------------|----------------------|
| `strategy.html` displays a valid VIX value within 5 seconds on standard broadband | Manual load test | Met. The cached and data-file paths are both synchronous, so the practical figure is well under 1 second |
| All five tier allocations render correctly, with the active tier highlighted in the reference table | Manual check against the table in this document | Met |
| The donut chart reflects the correct allocation percentages for the current tier | Visual check against the allocation table on the same page | Met |
| No layout breakage at 375px wide and above | Manual resize, or device emulation | Met |
| Zero JavaScript console errors on load in production | DevTools console on all three pages | Met as of v1.1.1, which fixed the last known error |
| The "Built by Azqato" footer credit and the Support nav link are present on all pages | Grep or visual check | Met on all three pages |
| The risk disclaimer is visible without scrolling on `index.html` and `strategy.html` | Visual check at the top of the page | **Not met.** See below |

> **Discrepancy, preserved rather than corrected.** The last criterion does not describe
> the current site. On `index.html` the risk disclosure is the fifth section, far below
> the fold, and the footer disclaimer is lower still. On `strategy.html` the disclaimer
> appears only inside the collapsed strategy accordion and in the footer, both below the
> fold. On `custom.html` it appears in the footer and in a table note. The intent behind
> the criterion, which matches tenet 5, is clearly that risk language must not be buried
> or dismissible, and it is not buried: every page carries it in plain text with no
> dismiss control. But "without scrolling" is factually not true of any of the three
> pages. Either the criterion should be reworded to match the intent, or the pages should
> carry a disclaimer above the fold. This is a product decision, logged as open
> question 3.

---

## Tenets

Tenets are ordered by priority. When two conflict, the higher one wins.

### 1. The Rule Is the Product

The strategy's value is its mechanical, unemotional nature. Every feature decision must
reinforce rule-following, not discretion. If a feature invites users to override the
allocation, tune parameters, or second-guess the current tier, it works against the
product. Features that add "flexibility" at the cost of rules-based discipline are
rejected. This tenet is the one that forces the removal of popular power-user features
that would undermine the core thesis, which is exactly why it is first.

Note the tension the Custom builder (v1.2.0) creates with this tenet, and the resolution
that was chosen: the user may change *which instrument* fills each risk slot, but not the
tier boundaries and not the percentages. Substitution is a portability feature. Tuning
the rule would not have been allowed.

### 2. Show the Signal Instantly

A blank screen while waiting for the network costs user trust. The last-known VIX value
is always available from `localStorage` and must be painted synchronously on every page
load, before any async operation resolves. No feature may hold the primary display
hostage to a network call. If the screen can be populated from cache, it must be,
immediately.

### 3. Zero Trust in the Data Pipeline

The VIX data source (Yahoo Finance, fetched server-side by a scheduled GitHub Actions
workflow, with a direct browser and CORS-proxy fallback) has no SLA and is not under our
control. Every code path that depends on that data must handle failure gracefully: fall
back to the cached value first, then to an explicit error state. The user must always see
the most useful available information, never a silent failure and never a broken layout.

### 4. No Backend, No Exceptions

Adding a persistent server introduces cost, maintenance burden, security surface, and
operational risk. Every feature must be implementable in a browser with zero server-side
execution *at request time*. The one exception, introduced in v1.1.0, is a scheduled
GitHub Actions workflow that runs independently of any user request and only writes a
static file (`data/vix.js`) back to the repository. It never serves a live request, has
no API, and holds no state beyond that one file. This is deliberately distinct from a
backend: there is nothing running that a user request ever reaches. If a proposed feature
requires code that responds to a live user request, it belongs in a post-v2.0 scope or
must be redesigned around browser-only primitives.

### 5. The Disclaimer Is Non-Negotiable

Leveraged ETF strategies carry extreme risk. The legal and ethical obligation to display
a clear, legible risk disclaimer is not a UX feature that can be minimized, collapsed, or
de-prioritized to improve conversion. It must appear on every page that references TQQQ,
in legible text, before the user reaches any call to action.

Two current facts sit uneasily against this tenet and are recorded rather than smoothed
over: the disclaimer is below the fold on all three pages (see
[Success Criteria](#success-criteria)), and the `--text-muted` color used for the
disclaimer text fails WCAG AA contrast (see DESIGN.md). "Legible" is not currently
satisfied in the strict sense.

---

## Roadmap

### Current Phase

**Phase 2, server-side data plus custom strategies (v1.1.x to v1.2.x).**
The MVP (v1.0.x) is complete and deployed. VIX data refreshes on a schedule via GitHub
Actions in addition to the browser fallback fetch. The Custom strategy builder (v1.2.0)
has shipped with free-text ticker entry. The backlog was reprioritized on 2026-07-09 to
favor data-visualization features (sparkline, percentile rank) ahead of strategy variants
and ticker verification. v1.2.1 is a documentation-only release that consolidated ten
documents into four.

Phase 2 is not closed. Before phase 3 starts, the operational question about the data
pipeline (open question 1) needs an answer, because every planned feature from v1.3.0
onward assumes reliable historical and current data.

### Milestone Table

| Milestone | Version | Status | Date |
|-----------|---------|--------|------|
| Initial scaffolding and documentation | v1.0.0 | Complete | 2026-06-06 |
| Full HTML, CSS, and JS implementation | v1.0.1 | Complete | 2026-06-06 |
| CORS proxy fix (allorigins.win) | v1.0.2 | Complete | 2026-06-06 |
| Dashboard boot fix (module race condition) | v1.0.3 | Complete | 2026-06-06 |
| localStorage migration plus `getCachedVIX()` | v1.0.4 | Complete | 2026-06-06 |
| Documentation update | v1.0.5 | Complete | 2026-06-06 |
| Cache TTL increased to 30 minutes | v1.0.6 | Complete | 2026-06-06 |
| Eastern Time timestamp display | v1.0.7 | Complete | 2026-06-06 |
| Emoji favicon | v1.0.8 | Complete | 2026-06-06 |
| Footer credit plus Support nav link | v1.0.9 | Complete | 2026-06-07 |
| Navigation rebuild with active-page highlight | v1.0.10 | Complete | 2026-06-07 |
| Full documentation audit, `/docs` created | v1.0.11 | Complete | 2026-06-08 |
| Loading counter and error state | v1.0.12 | Complete | 2026-06-15 |
| Azqato Invests nav link | unversioned | Complete | 2026-06-15 or later. Shipped in commit `c0d0477` with no patch notes entry. See D-08 |
| Server-side VIX data pipeline (GitHub Actions) | v1.1.0 | Complete | 2026-07-09 |
| Fix inline-script global scope collision | v1.1.1 | Complete | 2026-07-09 |
| Custom strategy builder, free-text tickers | v1.2.0 | Complete | 2026-07-09 |
| Documentation consolidation to four files | v1.2.1 | Complete | 2026-08-25 |
| 12-month historical VIX sparkline | v1.3.0 | Planned | TBD |
| VIX percentile rank mode | v2.0.0 | Planned | TBD |
| QQQ against 200-day MA trend filter | v2.1.0 | Planned | TBD |
| SMH and SOXL strategy toggle | v2.2.0 | Planned | TBD |
| Live ticker verification against a real quote | v2.3.0 | Planned | TBD |
| Backtesting visualization | v3.0.0 | Planned | TBD |
| Full portfolio tracker | v4.0.0 | Planned | TBD |

No milestone currently carries a target date. The project is maintained by one person
with no external commitment, so dates would be fiction. Status values in use are
Complete, Planned, In Progress, and Blocked; nothing is currently In Progress or Blocked.

### Feature Breakdown Per Milestone

#### v1.1.0, server-side VIX data pipeline plus `file://` support. Complete

- New scheduled GitHub Actions workflow (`update-vix.yml`) fetches the VIX directly from
  Yahoo Finance's `v8/finance/chart/^VIX` endpoint server-side (no CORS proxy needed off
  the browser) and commits the result to `data/vix.js` as a `window.__VIX_DATA__ = {...}`
  assignment, not as JSON.
- Runs on 8 fixed cron schedules per weekday, hourly from 9:45am to 4:45pm ET. Fixed to
  EST (UTC-5) year-round rather than DST-aware, so during EDT (roughly March to November)
  every run lands one hour later in ET. The close-of-day run fires at 5:45pm ET instead
  of 4:45pm. Accepted tradeoff to keep the schedule simple and low frequency.
- `vix.js`, `strategy.js`, and `chart.js` converted from ES modules to classic
  `<script src>` tags with `window.*` namespaces. This was necessary, not cosmetic:
  browsers block `type="module"` scripts entirely under `file://`, so the site previously
  failed completely when opened by double-clicking `index.html`.
- `data/vix.js` is loaded via a plain `<script src>` rather than `fetch()`, since
  fetching a local file is separately blocked under `file://` even for classic scripts.
- Falls back to the existing allorigins.win proxy fetch only if `window.__VIX_DATA__` is
  unavailable.
- Requires `permissions: contents: write` on the workflow. Failed fetches skip the commit
  and let the next scheduled run retry.
- `actions/checkout` bumped from v4 to v7 to target Node 24 natively.

#### v1.1.1, fix inline-script global scope collision. Complete

- The v1.1.0 conversion introduced a regression: the inline boot scripts destructured
  `getCachedVIX`, `fetchVIX`, `getTier`, and others as top-level `const`, but classic
  scripts share one global scope and `vix.js` and `strategy.js` already declared
  top-level `function` bindings with those exact names. The redeclaration threw a
  `SyntaxError` on every page load, killing the entire inline script before any DOM
  update ran.
- Symptom: the VIX value stuck on `--`, no loading counter, no tier badge, on GitHub
  Pages, `file://`, and local servers alike, including incognito, which ruled out a
  caching explanation.
- Fixed by wrapping each inline script's content in an IIFE, giving its `const`
  declarations their own scope so they shadow the globals instead of colliding.

#### v1.2.0, custom strategy builder. Complete

- New `custom.html` page, added to the nav on every page as **Custom**, between Dashboard
  and Azqato Invests.
- Four fixed risk categories seeded from the core strategy: Risk Off (default BIL),
  Diversify (default SPY), Risk On (default QQQ), Full Risk (default TQQQ), each with a
  free-text input.
- Input is free-text and not verified against a live quote. It is sanitized client-side
  (uppercased, restricted to `[A-Z0-9.-]`, capped at 10 characters) as hygiene only. The
  UI explicitly says tickers are not verified, so it is not mistaken for validation.
- Recomputes the allocation using the same tier boundaries and weights as the core
  strategy, through the unmodified `getAllocation()`. Only the displayed ticker changes,
  never the math.
- Selections persist in `localStorage['vix_custom_tickers']` only.
- `initChart()` and `updateChart()` extended with an optional `labels` parameter so the
  donut can show custom symbols, defaulting to the original four when omitted.
- All user-entered text is rendered with `document.createElement()` and `.textContent`,
  never `innerHTML`.

#### v1.2.1, documentation consolidation. Complete

- Ten documents reduced to four: `README.md` at the root, and `PRD.md`, `DESIGN.md`,
  `PATCHNOTES.md` in `/docs`.
- Seven standalone documents folded into this file. See
  [Deprecation and Removal](#deprecation-and-removal) for the mapping.
- `README.MD` renamed to `README.md` and rewritten for a general reader rather than a
  developer, with all developer content moved into the [Runbook](#runbook) and
  [Technical Requirements](#technical-requirements) sections here.
- New sections written from scratch during the audit: Conventions, Writing Style, Browser
  Testing, Deprecation and Removal, Documentation Versus Reality, Risks and Open
  Questions, Working Practice.
- No code changed.

#### v1.3.0, historical VIX sparkline

- Render a 12-month Chart.js line chart on `strategy.html`.
- Show the five tier boundaries as horizontal reference lines.
- Mark the current VIX value with a distinct point.
- Data from the Yahoo Finance historical daily endpoint. Open design question: whether to
  fetch this live through the proxy or extend `update-vix.yml` to commit a history file,
  the latter being more consistent with the current architecture.

#### v2.0.0, VIX percentile rank

- Fetch 252 trading days of VIX history.
- Compute the current value's percentile rank against the trailing year.
- Display as a supplemental badge next to the current VIX value on `strategy.html`.
- Adds context for whether the current reading is historically high or low.

#### v2.1.0, trend filter

- Fetch QQQ's 200-day moving average.
- Optional toggle: if QQQ is below its 200-day MA, suppress the TQQQ allocation and
  redistribute it to QQQ.
- Display the MA relationship as a supplemental indicator.
- Opt-in. Default behavior unchanged. Note that this is the first planned feature that
  lets the user alter the allocation, which puts it in direct tension with tenet 1; it is
  allowed only because it is a rule, not a dial.

#### v2.2.0, SMH and SOXL strategy toggle

- Add a second allocation table: QQQ, SMH, TQQQ, SOXL.
- UI toggle between "Core Strategy" and "Growth Rocket" on `strategy.html`.
- The Growth Rocket table as drafted uses different tier boundaries from the core
  strategy, so `getTier()` would need to accept a tier set. See open question 6.

#### v2.3.0, live ticker verification

- On blur or an explicit action, verify the entered ticker resolves to a quote, reusing
  the Yahoo Finance `v8/finance/chart/<TICKER>` endpoint through the allorigins.win proxy.
- Show inline state: `Verifying...`, then valid (surfacing the resolved fund name) or
  invalid (`Ticker not found`).
- Debounce input and cache verified tickers for the session.
- Degrade gracefully if the proxy is unavailable. Do not hard-block saving, since the
  proxy has no SLA.
- Does not attempt to confirm the ticker is an ETF rather than a single stock, or a
  sensible fit for its category. Yahoo's response does not cleanly distinguish instrument
  type, so the scope stays at "does this symbol exist and trade".

#### v3.0.0, backtesting visualization

- Fetch multi-year daily VIX and ETF price history.
- Simulate the strategy's portfolio value over time against SPY buy-and-hold.
- Render as a Chart.js multi-line chart.
- The largest feature to date. Requires significant in-browser data processing and would
  be the first thing to seriously test the no-backend tenet.

#### v4.0.0, portfolio tracker

- The user enters current holdings (ticker, and shares or dollar value).
- The app computes the target allocation in dollars and the delta from the current state,
  for example "buy $X of TQQQ, sell $Y of BIL".
- Requires persistent user data. A browser-only localStorage approach is preferred to
  avoid adding a backend.
- Significant UX complexity. Scope needs further definition before starting.

### Explicitly Deferred

| Feature | Reason deferred |
|---------|----------------|
| Push or email notifications for tier changes | Requires a service worker plus either a backend or a third-party push service. Violates tenet 4. |
| Brokerage integration for auto-rebalance | Requires OAuth flows, per-broker API keys, and regulatory considerations far outside current scope. |
| Native mobile app | No evidence of demand. The web app is fully responsive. Build cost is not justified. |
| Dark and light mode toggle | The dark terminal aesthetic is intentional and core to the design identity. A light mode adds maintenance cost with no clear user benefit. |
| Multi-strategy comparison view | Adds UI complexity that risks confusing users who came for a single clear answer. Deferred until post-v2.0 when user research exists. |
| VIX-based options strategy guidance | Requires options-specific risk disclosure, regulatory complexity, and deeper financial expertise than the product currently has. |
| Analytics of any kind | Would require a consent flow to be consistent with the stated no-tracking promise, and the consent banner would cost more trust than the data is worth at current traffic levels. Revisit if traffic reaches a level where the data would change a decision. |
| A build step or framework | Would break `file://` support and the "the repository is the artifact" property that makes the project forkable. The cost of duplicated HTML chrome across three files is accepted in exchange. |

---

## Metrics

### North Star Metric

**Weekly unique visitors to `strategy.html`.**

`strategy.html` is the core product, the live allocation dashboard. A visitor who reaches
it is someone who found the tool useful enough to check their allocation. Visits to
`index.html` alone may be one-time reads. Repeat visits to `strategy.html` represent the
retained, high-value user.

**Important caveat.** This metric is currently unmeasurable. The project has no analytics
by deliberate choice (see Explicitly Deferred), and GitHub Traffic Insights reports
repository page views, not GitHub Pages site views, over a rolling 14-day window. Every
target below should be read as a stated intention rather than something being tracked.
This is logged as open question 5.

### Acquisition Metrics

| Metric | Definition | Target | Timeframe |
|--------|-----------|--------|-----------|
| Monthly unique visitors | Unique sessions across `index.html`, `strategy.html`, `custom.html` | 500 per month | 3 months post-launch |
| GitHub referral traffic | Sessions originating from GitHub | 30 percent or more of total | Ongoing |
| Organic search sessions | Sessions arriving via a search engine | 100 per month | 6 months post-launch |
| GitHub stars | Total stars on the repository | 25 | 6 months post-launch |

**Measurement method.** GitHub Insights provides referral and traffic data for the
repository. Site page-view analytics would require adding a privacy-respecting script
such as Plausible or GoatCounter to all pages, which is currently deferred. Until then,
star and fork counts are the only real acquisition signal.

### Engagement Metrics

| Metric | Definition | Target | Timeframe |
|--------|-----------|--------|-----------|
| `strategy.html` visit rate | Percentage of sessions that include `strategy.html` | 60 percent or more | Ongoing |
| Average session duration | Time from first to last recorded page event | Above 90 seconds | Ongoing |
| Manual refresh rate | Refresh button clicks per session | Diagnostic only, no target | Ongoing |
| Custom page save rate | Percentage of `custom.html` visitors who save a configuration | No target set yet | Ongoing |

**Measurement method.** Requires an analytics tool with page-view and click-event
tracking. Not currently possible.

### Retention Metrics

| Metric | Definition | Target | Timeframe |
|--------|-----------|--------|-----------|
| Returning visitor rate | Percentage of sessions from a previously seen visitor | 30 percent or more | 6 months post-launch |
| Weekly active users | Unique visitors in any rolling 7-day window | 100 per week | 6 months post-launch |

**Measurement method.** Requires analytics with return-visit identification. A weak local
proxy exists: the presence of `localStorage['vix_last_known']` in a browser indicates at
least one prior visit, but that value is only visible to the user's own browser and is
never transmitted, so it cannot be aggregated. It is a diagnostic a user could check, not
a metric the project can collect.

### Performance Metrics

| Metric | Definition | Target | Measurement method |
|--------|-----------|--------|--------------------|
| VIX value display time | Time until any value, cached or from the data file, appears in the DOM | Under 200ms | DevTools performance timeline |
| Largest Contentful Paint | Time until the main content renders | Under 1.5s | Lighthouse audit |
| Data fetch latency | Time from page load to a resolved VIX value | Under 5s | Console timing |
| Console errors on load | JavaScript errors in production | 0 | DevTools console, manual per release |
| Total page weight | Bytes transferred on a cold load | Under 250KB | DevTools network panel |

> **Contradiction, resolved.** The retired `TRD.md` set the cached-display target at
> under 100ms while the retired `METRICS.md` set it at under 200ms. Both were aspirational
> and neither was measured. 200ms is retained here as the single figure, because it is the
> looser of the two and both paths (localStorage read, and the `window.__VIX_DATA__`
> global read) are synchronous and will comfortably beat it anyway.

### Targets Summary

| Metric | Current | 90-day target |
|--------|---------|---------------|
| Weekly unique visitors to `strategy.html` | Unknown, unmeasurable | 100 per week |
| GitHub stars | Unknown, check the repository | 10 |
| Returning visitor rate | Unknown, unmeasurable | 25 percent |
| Largest Contentful Paint | Roughly 0.8s by prior manual measurement | Under 1.5s |
| Console errors on production load | 0 | 0 |

### Measurement Method Summary

| Layer | Tool | Status |
|-------|------|--------|
| Repository views, referrers | GitHub Traffic Insights | Available, aggregate, 14-day window, repository not site |
| Stars, forks, watchers | GitHub repository stats | Available |
| Page performance | Lighthouse in Edge DevTools | Manual, per release |
| Functional correctness | The manual checklist in [Working Practice](#working-practice) | Per release |
| Site sessions, duration, events | Not implemented | Requires an analytics script, currently deferred |

### Reporting Cadence

| Cadence | What to review |
|---------|----------------|
| Weekly | GitHub Traffic Insights, and whether `update-vix.yml` runs are succeeding |
| Monthly | GitHub stars, forks, open issues |
| Per release | Lighthouse score, console error check, manual smoke test on mobile and desktop |

---

## Runbook

Everything needed to run, build, deploy, and recover the project. Assume the reader has
just cloned the repository and has nothing else installed.

### Prerequisites

| Requirement | Version the project needs | Purpose |
|------------|---------------------------|---------|
| Git | Any version from 2.20 onward | Clone and manage the repository |
| A modern browser | Any release from 2022 onward | Run the site. Requires support for `localStorage`, optional chaining, nullish coalescing, `clamp()`, CSS custom properties, and `100svh` |
| Microsoft Edge | Any current version | The designated browser for any automated or headless testing. See [Browser Testing](#browser-testing) |

There is deliberately **no** Node.js, npm, `package.json`, lock file, bundler,
transpiler, or local HTTP server requirement. If a future change introduces one, that is
a significant architectural decision and belongs in this document before it belongs in
the repository.

### Local Setup

```bash
git clone https://github.com/Azqato/vix.git
cd vix
```

Then open `index.html` directly in a browser: double-click it, drag it into a browser
window, or use `File > Open`. There is nothing to install and no server to start.

A local HTTP server works identically if preferred:

| Method | Command | Default URL |
|--------|---------|-------------|
| Python 3 | `python -m http.server 8080` | `http://localhost:8080` |
| Node http-server | `npx http-server -p 8080` | `http://localhost:8080` |
| VS Code Live Server | Right-click `index.html`, Open with Live Server | `http://127.0.0.1:5500` |

**There is no default port**, because there is no server in the default workflow. The
ports above belong to whichever optional tool is used.

#### Why `file://` works

Through v1.0.x, `vix.js`, `strategy.js`, and `chart.js` used ES modules
(`type="module"`), which all modern browsers block over the `file://` protocol.
Double-clicking `index.html` silently failed to load any JavaScript. As of v1.1.0 those
files are classic scripts that attach to `window.*` namespaces, which browsers load
fine under `file://`. The VIX value comes from `data/vix.js`, loaded as a plain
`<script src>` rather than `fetch()`, because fetching a local file is separately blocked
under `file://` even for classic scripts.

One consequence worth knowing: under `file://` the Chart.js CDN script still requires a
working internet connection. Opening the files offline gives a working `index.html` and a
working allocation table on `strategy.html`, but a blank chart canvas.

### Build

There is no build step. The repository is the deployable artifact. No compilation,
bundling, transpilation, or minification is used, and the output directory is the
repository root itself.

### Deploy

#### Production (GitHub Pages)

1. Commit the changes:
   ```bash
   git add <files>
   git commit -m "type: description"
   ```
2. Push to `main`:
   ```bash
   git push origin main
   ```
3. GitHub Pages deploys automatically, typically within 1 to 3 minutes.
4. Verify at `https://azqato.github.io/vix/`.
5. If the site does not update, check the Actions tab on GitHub for the `pages-build-deployment`
   run.

There are no manual steps beyond the push. There is no staging environment: `main` is
the live site, so everything must be tested locally before pushing.

#### First-time GitHub Pages setup

Required once per repository, and already done for this one:

1. Repository Settings, then Pages.
2. Source: `Deploy from a branch`.
3. Branch: `main`, folder: `/ (root)`.
4. Save. GitHub shows the published URL once the first deployment completes.

### Rollback

Revert the most recent commit:

```bash
git revert HEAD
git push origin main
```

Revert to a specific earlier state:

```bash
git log --oneline
git revert <commit-sha>
git push origin main
```

GitHub Pages redeploys automatically after the push, so the rollback is live within 1 to
3 minutes.

**Do not use `git reset --hard` on `main`.** It rewrites history and requires a force
push, which is disruptive and can break the Pages deployment history. `git revert` is the
project's rollback mechanism because it is additive: it leaves the record of what
happened intact, which matters more here than a clean history, given the changelog is a
product artifact.

Note that a revert touching `data/vix.js` will be overwritten by the next successful
`update-vix.yml` run, which is the desired behavior.

### Environment Configs

| Environment | URL | Branch | Deploy method | Differences |
|-------------|-----|--------|---------------|-------------|
| Production | `https://azqato.github.io/vix/` | `main` | Automatic on every push | The only environment with a live scheduled data refresh |
| Local, from disk | `file://.../vix/index.html` | Any | None | Chart.js still loads from the CDN, so it needs a connection. `localStorage` is scoped to the `file://` origin, so it is a separate cache from the production site |
| Local, over HTTP | `http://localhost:8080` or similar | Any | None | `localStorage` is scoped to `localhost`, again a separate cache |

There is no staging environment and no plan to add one. Two consequences follow: test
locally before pushing, and remember that a cache behaving differently between local and
production is usually just the three separate `localStorage` origins.

### Environment Variable Reference

**There are no environment variables.** The application has no backend, no build step,
and no secrets. There is no `.env` file, no API key, no database credential, and no
configuration value that must be protected.

| Key | Purpose | Required |
|-----|---------|----------|
| (none) | | |

The one credential in the system is `GITHUB_TOKEN`, which GitHub Actions injects
automatically into the `update-vix.yml` workflow run. It is never declared, never stored,
and never appears in the repository. The workflow declares `permissions: contents: write`
to scope what that token may do.

The Yahoo Finance endpoint and the allorigins.win proxy URL are public, unauthenticated,
and intentionally visible in source. Nothing that resembles a secret should ever be
committed.

### Common Errors

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| VIX shows `--`, no tier badge, no chart | A required `<script>` tag failed to load, or an inline script threw before its first DOM write | Open DevTools console. Verify `data/vix.js`, `assets/js/vix.js`, `assets/js/strategy.js`, `assets/js/chart.js`, and on `custom.html` `assets/js/custom.js`, all load without error and in that order |
| `Identifier 'X' has already been declared` in console | An inline script declared a top-level `const` with the same name as a global `function` from one of the library files | Wrap the inline script in an IIFE. This is the v1.1.1 fix; do not remove those IIFEs |
| VIX shows a `STALE` badge | `window.__VIX_DATA__` was unavailable and the allorigins.win proxy was also unreachable | Click Refresh. Check `https://azqato.github.io/vix/data/vix.js` loads, and check the Actions tab for `update-vix.yml` failures |
| `data/vix.js` not updating during market hours | An `update-vix.yml` run failed, the Yahoo endpoint changed, or the repository went 60 days without a commit and GitHub auto-disabled the schedule | Check the Actions tab. Re-run manually via `workflow_dispatch`. Push any commit to re-enable a disabled schedule |
| VIX shows an `ERROR` badge and `Error Please Refresh` | Every data path failed and there is no prior cached value | Check the network connection, then click Refresh |
| The displayed VIX value is days old but the badge says `LIVE` | Expected behavior with the current design. `LIVE` means "read this page load from the data file or the proxy", not "quoted in the last minute". The value is only as fresh as the last successful workflow run | Check the timestamp text next to the badge, which shows the real quote time. See D-01 |
| Chart canvas is blank but the value and tables render | The Chart.js CDN failed to load, or the browser is offline while running from `file://` | Check `https://status.jsdelivr.com`. Verify the CDN URL is reachable |
| `window.Chart is not defined` | The Chart.js CDN `<script>` did not execute before `chart.js` ran | Ensure the CDN script tag stays first among the scripts at the end of `<body>` on `strategy.html` and `custom.html` |
| `Cannot read properties of undefined (reading 'TICKERS' / 'getCachedVIX' / 'CATEGORIES')` | A script tag is missing or out of order | Confirm the order: `data/vix.js`, `assets/js/vix.js`, `assets/js/strategy.js`, `assets/js/chart.js`, `assets/js/custom.js`, then the inline script |
| `Unexpected VIX response shape` in console | Yahoo Finance changed the response structure | Update `parseResponse()` in `assets/js/vix.js` to match the new field path, and update the `jq` filters in `update-vix.yml` to match |
| Navigation active highlight is on the wrong page | `nav-active` is on the wrong `<a>` in that file's hardcoded nav | Each page sets `nav-active` and `aria-current="page"` on its own link: About in `index.html`, Dashboard in `strategy.html`, Custom in `custom.html` |
| Cached VIX is hours or days old in the browser | The 30-minute TTL expired but every data path returned the same old value, or the cache was never cleared | Click Refresh, which removes `localStorage['vix_last_known']` and re-runs the fetch |
| Custom page shows a stale ticker after Save | The re-render did not run, or an error interrupted the submit handler | Check the console. Confirm `localStorage['vix_custom_tickers']` actually updated |
| Custom page chart and table stay on "Loading allocation data" | No VIX cache existed at boot, so `paintCached()` returned early, and the subsequent fetch has not resolved or failed | Wait for the fetch, or click Refresh. If it never resolves, this is the `ERROR` path above |
| Site deploys but shows the old version | Browser or CDN cache | Hard refresh with `Ctrl+Shift+R` or `Cmd+Shift+R`. The GitHub Pages CDN clears within a few minutes of deploy |

### Monitoring

There is no server-side monitoring, because there is no server. All monitoring is manual.

| Check | How | When |
|-------|-----|------|
| The site loads and shows a value | Open `https://azqato.github.io/vix/strategy.html` and confirm a number and a tier banner appear | Per release, and after any change to `vix.js` |
| `update-vix.yml` runs are succeeding | Actions tab, filter by "Update VIX Data" | Weekly. This is the single most important recurring check, and the one that would have caught open question 1 |
| `data/vix.js` is current | Open `https://azqato.github.io/vix/data/vix.js` and compare `fetchedAt` and `timestamp` to the current time during market hours | Weekly, or whenever the display looks stale |
| No console errors | DevTools console on all three pages | Per release |
| Mobile rendering | Test at a 375px viewport width | Per release |
| GitHub Pages deployment succeeded | Actions tab | After every push to `main` |
| allorigins.win is operational | Visit `https://api.allorigins.win/` | Only when investigating a STALE report |
| Chart.js CDN is reachable | Visit the pinned CDN URL, or `https://status.jsdelivr.com` | Only when the chart is blank |

There are no uptime alerts, no error reporting service, and no log aggregation. A failure
is discovered either by the maintainer opening the site or by a user reporting it. That
is a known and accepted limitation of a zero-cost, zero-backend deployment, and it is why
the weekly Actions check matters.

---

## Technical Requirements

### System Architecture

VIX Strategy is a fully static, browser-only application deployed on GitHub Pages. There
is no server, no database, no build pipeline, and no Node.js runtime for the site itself.
The one piece of server-side execution is a scheduled GitHub Actions workflow that runs
independently of any user request, fetches the VIX, and commits the result to the
repository as a static file.

```
GitHub Actions (.github/workflows/update-vix.yml)
  8 fixed cron runs per weekday, EST basis
    |
    +-- [External] Yahoo Finance JSON API
    |     (direct call, no CORS restriction server-side)
    |
    +-- commits data/vix.js to main
              |
              v
        GitHub Pages (static file serving from main, root)
              |
              v
Browser: index.html | strategy.html | custom.html
    |
    +-- assets/css/styles.css        all styling, one file
    +-- data/vix.js                  sets window.__VIX_DATA__
    +-- assets/js/vix.js             window.VixData, data read + localStorage cache
    +-- assets/js/strategy.js        window.VixStrategy, tier logic + allocations
    +-- assets/js/chart.js           window.VixChart, Chart.js wrapper  (strategy, custom)
    +-- assets/js/custom.js          window.VixCustom, categories + config  (custom only)
    +-- inline <script>              per-page boot, wrapped in an IIFE
    |
    +-- [CDN] Chart.js 4.4.0 from jsdelivr.net   (strategy, custom)
    +-- [External, fallback only] Yahoo Finance via allorigins.win
```

Every script is a plain classic `<script src="...">` tag, in the order shown, followed by
the inline boot script, all at the end of `<body>`. None uses `type="module"`. Each file
attaches its exports to a `window.*` namespace rather than using `import` and `export`.

This is deliberate. `type="module"` scripts are blocked entirely by browsers under
`file://`, which broke the site whenever someone opened `index.html` directly instead of
through a server. Classic scripts execute identically under `file://`, `http://localhost`,
and GitHub Pages. The same reasoning is why `data/vix.js` is a script that assigns a
global rather than a JSON file that gets fetched: `fetch()` of a local file is blocked
under `file://` even for classic scripts.

The cost of that choice is that all top-level `function` and `const` declarations in
these files share one global scope, which is exactly what caused the v1.1.1 bug. Every
inline boot script is therefore wrapped in an IIFE, with a comment explaining why. Do not
remove those wrappers.

#### Data flow on each page load

1. `getCachedVIX()` reads `localStorage` synchronously. Zero network latency.
2. If a cached value exists it is painted immediately, before anything asynchronous runs.
3. `fetchVIX()` runs. If the cached value is under 30 minutes old it returns that value
   without touching anything else. Otherwise it reads `window.__VIX_DATA__`, set
   synchronously by `data/vix.js`, with no network call and no CORS concern. If that
   global is unavailable it falls back to the CORS-proxied Yahoo Finance request.
4. `getTier(vixValue)` maps the number to one of five string keys, `tier1` through
   `tier5`.
5. `getAllocation(tier)` returns a normalized `{ BIL, SPY, QQQ, TQQQ }` percentage object.
6. The page renders: the VIX number, the status badge, the timestamp, the tier banner,
   the legend, the allocation table, the tier reference table (`strategy.html` only), and
   the donut chart (`strategy.html` and `custom.html`).

#### Data flow for `data/vix.js` itself

1. `.github/workflows/update-vix.yml` fires on one of 8 fixed weekday cron schedules, or
   on a manual `workflow_dispatch`.
2. The workflow calls the Yahoo Finance endpoint directly from the runner with a
   `Mozilla/5.0` user agent, trying `query1` then `query2`, extracting
   `regularMarketPrice` and `regularMarketTime` with `jq`.
3. On success it writes `data/vix.js` as a `window.__VIX_DATA__ = {...}` assignment and
   commits with the message `chore: update VIX data [skip ci]`, but only if the file
   actually changed.
4. On failure the fetch step exits non-zero. `continue-on-error: true` keeps the job
   green, and both subsequent steps are gated on `steps.fetch.outcome == 'success'`, so
   they are skipped and the previous value stays live until the next run.

### Tech Stack

| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| Markup | HTML5 | Living standard | Three pages: `index.html`, `strategy.html`, `custom.html` |
| Styling | CSS3 with custom properties | Living standard | A single file, `assets/css/styles.css`, roughly 1000 lines |
| Logic | Vanilla JavaScript | ES2020 or later | Uses optional chaining, nullish coalescing, numeric separators (`60_000`), and `Object.entries`. Classic scripts, `window.*` namespaces |
| Charts | Chart.js | 4.4.0, pinned | Loaded as a synchronous CDN UMD `<script>` tag |
| VIX data, primary | `data/vix.js` in the repository | n/a | `window.__VIX_DATA__` global, loaded via `<script src>`. No browser network call |
| VIX data, fallback | Yahoo Finance JSON API | v8 | Not directly browser-accessible. Requires a CORS proxy. Used only if the global is unavailable |
| CORS proxy | allorigins.win | n/a | Public, unauthenticated relay. Fallback path only |
| Scheduled refresh | GitHub Actions | `actions/checkout@v7`, `ubuntu-latest` | 8 fixed cron runs per weekday |
| Hosting | GitHub Pages | n/a | Serves static files from the root of `main` |
| Version control | Git and GitHub | n/a | Single `main` branch, no tags |

There are zero installed dependencies. The only third-party code that runs in the browser
is Chart.js, and it arrives from a CDN at runtime rather than from the repository.

### Folder Structure

```
vix/
├── index.html                  Pitch page. Explains the strategy rationale. Hero gauge,
│                               problem cards, insight stats, full tier table, thesis
│                               cards, risk disclosure, footer CTA. Inline boot script
│                               at the end of body.
├── strategy.html               Live dashboard. VIX feed, tier banner, donut chart,
│                               allocation table, tier reference table, strategy
│                               accordion. Inline boot script at the end of body.
├── custom.html                 Custom strategy builder. Ticker form, VIX feed, tier
│                               banner, donut chart, allocation table. Inline boot
│                               script at the end of body.
├── README.md                   Public front door, written for a general reader.
│                               Deliberately carries no setup or technical detail.
├── data/
│   └── vix.js                  window.__VIX_DATA__ = { value, timestamp, fetchedAt }.
│                               Written only by update-vix.yml. Loaded as a script, not
│                               fetched. Committed to the repository.
├── .github/
│   └── workflows/
│       └── update-vix.yml      Scheduled Yahoo Finance fetch, commits data/vix.js.
│                               8 weekday cron entries plus workflow_dispatch.
├── .claude/
│   └── settings.local.json     Local Claude Code permission allowlist. Not part of the
│                               site. Currently allows one read-only shell command.
├── assets/
│   ├── css/
│   │   └── styles.css          Every style in the project. Banner-comment sections.
│   │                           Media queries collected at the bottom.
│   ├── js/
│   │   ├── vix.js              window.VixData. getCachedVIX(), fetchVIX(). Reads the
│   │   │                       data-file global, manages the 30-minute localStorage
│   │   │                       cache, falls back to the CORS proxy.
│   │   ├── strategy.js         window.VixStrategy. RAW tier weights, normalize(),
│   │   │                       getTier(), getAllocation(), getTierLabel(), TICKERS,
│   │   │                       ALL_TIERS. The single source of truth for the strategy.
│   │   ├── chart.js            window.VixChart. initChart(), updateChart(), and the
│   │   │                       centerText Chart.js plugin.
│   │   └── custom.js           window.VixCustom. CATEGORIES, getCustomConfig(),
│   │                           saveCustomConfig(), resetCustomConfig(),
│   │                           sanitizeTicker().
│   └── img/
│       ├── logo.svg            "VIX" wordmark in mono green with a volatility-spike
│       │                       polyline. Inline text and polyline, no raster.
│       └── favicon.svg         An SVG wrapping the chart-increasing emoji.
└── docs/
    ├── PRD.md                  This file. Product, roadmap, metrics, runbook,
    │                           architecture, conventions, security, FAQ.
    ├── DESIGN.md               The visual system: tokens, type, spacing, breakpoints,
    │                           components, accessibility, motion.
    └── PATCHNOTES.md           Dated changelog, every version.
```

Files that do not exist and are intentionally absent: `package.json`, any lock file, any
config file for a bundler, linter, or formatter, any test directory, `LICENSE`,
`CONTRIBUTING.md`, and `CLAUDE.md`. The absence of a `LICENSE` file is worth noting: the
project is described in its own FAQ as open source, but without a license file the
default is all rights reserved. Logged as open question 2.

### Data Models

#### VIX data file, `data/vix.js`

```javascript
window.__VIX_DATA__ = {
  "value": 15.84,
  "timestamp": "2026-07-09T20:15:01.000Z",
  "fetchedAt": 1783631218669
};
```

| Field | Type | Description |
|-------|------|-------------|
| `value` | number | `regularMarketPrice` from Yahoo Finance |
| `timestamp` | ISO 8601 string | `regularMarketTime` from Yahoo Finance, converted from Unix seconds. This is when the market quoted the value |
| `fetchedAt` | Unix milliseconds | When the GitHub Actions run wrote the file. This is when the project learned the value |

The two time fields are not interchangeable. `timestamp` is what the UI shows the user.
`fetchedAt` is what the runbook checks to see whether the pipeline is alive. Note that
`fetchedAt` in this file is written but never read by any browser code; the cache writes
its own `fetchedAt` at read time.

#### VIX cache entry, `localStorage['vix_last_known']`

```json
{
  "value": 18.43,
  "timestamp": "2026-06-08T14:30:00.000Z",
  "fetchedAt": 1749302400000
}
```

| Field | Type | Description |
|-------|------|-------------|
| `value` | number | The VIX value |
| `timestamp` | ISO 8601 string | The market quote time, preserved from the source |
| `fetchedAt` | Unix milliseconds | When this browser saved the value. Drives the 30-minute TTL |

#### VIX result object, returned by `getCachedVIX()` and `fetchVIX()`

```typescript
// Success, whether live, from the data file, or from cache
{ value: number; timestamp: Date; fetchedAt?: number; fromCache: boolean; stale: boolean; }

// Total failure: no data file, no reachable proxy, no cache
{ value: null; timestamp: null; error: true; }
```

The `fromCache` and `stale` flags drive the status badge:

| `fromCache` | `stale` | Badge |
|-------------|---------|-------|
| `false` | `false` | LIVE |
| `true` | `false` | CACHED |
| any | `true` | STALE |
| `error: true` | n/a | ERROR |

#### Allocation object, returned by `getAllocation(tier)`

```typescript
{ BIL: number; SPY: number; QQQ: number; TQQQ: number; }
```

All four values are percentages summing to exactly 100. `custom.html` reuses this object
unmodified: the keys are fixed slots for the percentage math, and only the displayed
ticker per slot differs.

#### Custom ticker config, `localStorage['vix_custom_tickers']`

```json
{
  "RISK_OFF": "BIL",
  "DIVERSIFY": "SPY",
  "RISK_ON": "QQQ",
  "FULL_RISK": "TQQQ"
}
```

Each value is a user-entered ticker, sanitized by `sanitizeTicker()`: trimmed,
uppercased, stripped to `[A-Z0-9.-]`, capped at 10 characters, and falling back to that
category's default (BIL, SPY, QQQ, TQQQ respectively) if the result is empty or fails the
pattern. Sanitization is applied on read as well as on write, so a hand-edited
`localStorage` value cannot bypass it.

#### Tier key

`'tier1' | 'tier2' | 'tier3' | 'tier4' | 'tier5'`

| Key | VIX range | Label returned by `getTierLabel()` |
|-----|-----------|------------------------------------|
| `tier1` | Below 15 | `VIX < 15 - Low Fear (Complacency)` |
| `tier2` | 15 to under 25 | `VIX 15-25 - Moderate Fear` |
| `tier3` | 25 to under 35 | `VIX 25-35 - Elevated Fear` |
| `tier4` | 35 to under 45 | `VIX 35-45 - High Fear` |
| `tier5` | 45 and above | `VIX > 45 - Extreme Fear (Crisis)` |

`getTierLabel()` returns `'Unknown Tier'` for any unrecognized key, which cannot happen
through `getTier()` but protects the render path.

#### TICKERS object, `window.VixStrategy.TICKERS`

```typescript
{
  [symbol: string]: {
    symbol: string;       // "BIL"
    name: string;         // "SPDR Bloomberg 1-3 Month T-Bill ETF"
    description: string;  // one sentence, shown in the allocation table
    color: string;        // hex, e.g. "#3b82f6"
  }
}
```

#### CATEGORIES array, `window.VixCustom.CATEGORIES`

```typescript
Array<{
  slot: 'BIL' | 'SPY' | 'QQQ' | 'TQQQ';  // key into the allocation object
  key: 'RISK_OFF' | 'DIVERSIFY' | 'RISK_ON' | 'FULL_RISK';  // key into localStorage config
  label: string;        // shown to the user, e.g. "Risk Off"
  color: string;        // hex, duplicated from the ticker color
  description: string;  // one sentence, shown under the input and in the table
}>
```

#### ALL_TIERS array, `window.VixStrategy.ALL_TIERS`

```typescript
Array<{ key: string; label: string; raw: object; normalized: object; }>
```

Built once at load from `RAW`. Used only to render the tier reference table on
`strategy.html`.

### API Design (internal data flow)

There is no HTTP API of the project's own. The public functions on the four `window.*`
namespaces are the internal API, and they are documented here with inputs, outputs, and
failure behavior.

#### `window.VixData`

| Function | Input | Output | Failure behavior |
|----------|-------|--------|------------------|
| `getCachedVIX()` | none | The VIX result object with `fromCache: true` and a computed `stale` flag, or `null` | Returns `null` on missing key, malformed JSON, or a `localStorage` access error. Never throws |
| `fetchVIX()` | none | A promise resolving to a VIX result object | Returns the stale cache if every source fails and a cache exists, otherwise `{ value: null, timestamp: null, error: true }`. Never rejects |

Internal helpers, not exported: `parseResponse()` (throws on an unexpected shape),
`fetchFromURL()` (throws on a non-OK HTTP status), `getDataFileVIX()` (returns `null` if
`window.__VIX_DATA__` is missing or malformed), and `saveToCache()` (swallows quota and
access errors).

#### `window.VixStrategy`

| Function or value | Input | Output |
|-------------------|-------|--------|
| `getTier(vix)` | number | One of the five tier keys. Any number at or above 45, including infinity, returns `tier5` |
| `getAllocation(tier)` | tier key | The normalized allocation object. Returns `undefined` for an unknown key |
| `getTierLabel(tier)` | tier key | The display label, or `'Unknown Tier'` |
| `TICKERS` | n/a | The frozen-by-convention ticker metadata object |
| `ALL_TIERS` | n/a | The five-element array used for the reference table |

#### `window.VixChart`

| Function | Input | Output |
|----------|-------|--------|
| `initChart(canvasId, labels?)` | element id, optional label array | A Chart.js instance. Labels default to `['BIL','SPY','QQQ','TQQQ']` |
| `updateChart(instance, allocation, vixValue, labels?)` | the instance, an allocation object, a number or null, an optional label array | none. Mutates and redraws the chart |

`initChart` will throw if `window.Chart` is undefined (CDN failure) or if the canvas
element is missing. Neither is currently guarded, which is recorded as technical debt.

#### `window.VixCustom`

| Function or value | Input | Output |
|-------------------|-------|--------|
| `CATEGORIES` | n/a | The four-element category array |
| `getCustomConfig()` | none | A sanitized config object, always complete, falling back to defaults |
| `saveCustomConfig(config)` | a partial or complete config | The sanitized config that was stored. Storage errors are swallowed and the sanitized object is still returned |
| `resetCustomConfig()` | none | The default config, after removing the stored key |

#### External endpoints consumed

| Endpoint | Method | Caller | Success | Failure |
|----------|--------|--------|---------|---------|
| `https://query1.finance.yahoo.com/v8/finance/chart/%5EVIX` | GET | `update-vix.yml`, directly | JSON with `chart.result[0].meta.regularMarketPrice` and `.regularMarketTime` | Falls through to `query2` |
| `https://query2.finance.yahoo.com/v8/finance/chart/%5EVIX` | GET | `update-vix.yml`, directly | Same | The workflow exits 1, the commit steps are skipped |
| `https://api.allorigins.win/raw?url=<query1 URL>` | GET | Browser, fallback only | Relayed JSON, same shape | Falls through to the `query2` proxy URL |
| `https://api.allorigins.win/raw?url=<query2 URL>` | GET | Browser, fallback only | Same | `fetchVIX()` returns the stale cache or the error object |
| `https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js` | GET | Browser, `strategy.html` and `custom.html` | Sets `window.Chart` | Blank chart canvas, the rest of the page still works |

### State Management

There is no in-memory state object, no reactive framework, and no event bus. Each page
reads from storage and writes directly to the DOM.

| Location | Contents | Lifetime |
|----------|----------|----------|
| `localStorage['vix_last_known']` | VIX value, ISO timestamp, `fetchedAt` | Persists across tabs, pages, and browser restarts, until the Refresh button or the user clears it. Scoped per origin, so `file://`, `localhost`, and the production site each keep a separate copy |
| `localStorage['vix_custom_tickers']` | Four sanitized ticker strings | Persists until the Reset button or the user clears it. Read and written only by `custom.html` |
| `data/vix.js` in the repository, as `window.__VIX_DATA__` | VIX value, ISO timestamp, `fetchedAt` | Overwritten by each successful `update-vix.yml` run. Otherwise it is the last known value indefinitely |
| DOM | The rendered number, badge, timestamp, tier banner, chart data, table rows, form values | Page session only. Rebuilt on each load from cache, then the data file, plus the custom config on `custom.html` |
| Module-scope variables in each inline boot script | `chartInstance`, `loadingTimer`, `config` on `custom.html` | Page session only |

A consequence worth knowing: because the two `localStorage` keys are independent and the
custom config is never validated against the VIX cache, clearing one does not affect the
other. The Refresh button clears only the VIX cache; the Reset button clears only the
ticker config.

### Third-Party Integrations

#### GitHub Actions, `update-vix.yml`

| Attribute | Value |
|-----------|-------|
| Trigger | 8 fixed `schedule` cron entries on weekdays, plus `workflow_dispatch` |
| Schedule basis | Fixed to EST (UTC-5) year-round, intentionally not DST-aware |
| Nominal times during EST (November to March) | 9:45am, 10:45am, 11:45am, 12:45pm, 1:45pm, 2:45pm, 3:45pm, 4:45pm ET |
| Actual times during EDT (March to November) | The same list shifted one hour later, so the close-of-day run lands at 5:45pm ET |
| Runner | `ubuntu-latest` |
| Action versions | `actions/checkout@v7` |
| Permissions | `contents: write`, to commit `data/vix.js` back to `main` |
| Commit identity | `github-actions[bot]` |
| Commit message | `chore: update VIX data [skip ci]` |
| Failure mode | The fetch step fails, `continue-on-error` keeps the job green, and both later steps are skipped by their `if` condition |

The DST drift is an accepted tradeoff for a simple fixed schedule with no polling.

#### Yahoo Finance JSON API

| Attribute | Value |
|-----------|-------|
| Endpoint | `https://query1.finance.yahoo.com/v8/finance/chart/%5EVIX` |
| Fallback | `https://query2.finance.yahoo.com/v8/finance/chart/%5EVIX` |
| Authentication | None. Public, undocumented, unsupported |
| Fields used | `chart.result[0].meta.regularMarketPrice` (float), `chart.result[0].meta.regularMarketTime` (Unix seconds) |
| Direct browser access | Blocked by CORS |
| Direct server-side access | Not blocked. CORS is a browser policy, so the Actions runner calls it directly |
| User agent | The workflow sends `Mozilla/5.0`. The browser fallback sends whatever the browser sends |

#### allorigins.win CORS proxy

| Attribute | Value |
|-----------|-------|
| Endpoint | `https://api.allorigins.win/raw?url=<encoded target>` |
| Authentication | None. Free public service |
| SLA | None |
| Role | Browser fallback only, used when `window.__VIX_DATA__` is unavailable |
| Failure mode | `fetchVIX()` falls back to the cached value with `stale: true` |
| History | Replaced `corsproxy.io` in v1.0.2, which Yahoo Finance was blocking with HTTP 403 |

#### Chart.js CDN (jsDelivr)

| Attribute | Value |
|-----------|-------|
| URL | `https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js` |
| Version | Pinned to 4.4.0 |
| Authentication | None |
| Load order | A synchronous `<script>` tag placed before the project's own scripts, so `window.Chart` exists when `chart.js` runs |
| Failure mode | The canvas stays blank. The VIX value and both tables still render |
| Subresource Integrity | Not implemented. Known debt |

### Performance Requirements

| Metric | Target | Rationale |
|--------|--------|-----------|
| Any VIX value in the DOM from cache | Under 200ms | A synchronous `localStorage` read, so this is bounded by parse and paint |
| Page fully interactive | Under 1.5s on standard broadband | There is no JS framework to hydrate |
| Data resolution complete | Under 5s | Network-dependent only on the fallback path. The primary path is synchronous |
| Minimum supported viewport | 375px wide | Enforced by `body { min-width: 375px }` |
| Chart.js CDN load | Under 1s | Roughly 60KB gzipped from a pinned CDN URL |
| Total transferred bytes, cold load | Under 250KB | Three of the four largest items are the HTML file, the stylesheet, and Chart.js |
| Console errors on production load | 0 | |

There is no bundle-size budget in the usual sense, because there is no bundle. The
practical equivalent is that no page should load more than the one stylesheet, the four
project scripts, and Chart.js.

### Known Technical Debt

| Item | Current approach | Correct approach |
|------|-----------------|------------------|
| No Subresource Integrity on the Chart.js CDN tag | The version is pinned but there is no `integrity` attribute | Add `integrity="sha384-..."` with `crossorigin="anonymous"` so the browser rejects a modified bundle |
| No fallback when Chart.js fails to load | `initChart()` throws on `window.Chart` being undefined; the canvas stays blank | Detect the failure and render a text-only allocation display, or at minimum guard the call |
| The tier table on `index.html` is hardcoded HTML | The same twenty percentages exist both in `strategy.js` `RAW` and as literal text in `index.html` | Render it from `ALL_TIERS`, the way `strategy.html` already does. Today, changing the strategy silently leaves the pitch page wrong |
| Ticker colors exist in three places | CSS tokens, `TICKERS` in `strategy.js`, `CATEGORIES` in `custom.js` | Read from `getComputedStyle` at init, or accept the duplication and add a comment in each location pointing at the others |
| Page chrome is duplicated across three HTML files | Header, nav, and footer are copy-pasted | There is no correct fix that preserves the no-build constraint. Accepted. The mitigation is the checklist in [Working Practice](#working-practice) |
| Boot logic is duplicated across three inline scripts | `formatTimestamp`, `setStatus`, `updateVIXDisplay`, `startLoadingCounter`, and `stopLoadingCounter` are near-identical in `strategy.html` and `custom.html` | Extract to a fifth library file, for example `assets/js/dashboard.js`, exposing `window.VixDashboard` |
| Normalization is currently a no-op | Raw allocations already sum to 100, so `normalize()` changes nothing | Keep it. It is a guard, not dead code. Optionally add an assertion that the raw row sums to 100 |
| `custom.html` renders the tier banner differently from `strategy.html` | A raw `tier.replace('tier','')` instead of `getTierLabel()` | Use `getTierLabel()` on both |
| No automated tests | Zero test coverage of any kind | Add unit tests for `getTier()`, `normalize()`, and `sanitizeTicker()`. These are pure functions and would be trivial to cover. Doing so requires deciding whether to accept a `package.json`, which the no-build constraint currently forbids |
| Manual version tracking | Version numbers are updated by hand in several document headers | A single source of truth, plus git tags. The repository currently has no tags at all |
| `fetchedAt` in `data/vix.js` is written but never read | The browser recomputes its own `fetchedAt` when caching | Harmless. Keep it, since it is the field the runbook uses to check pipeline health |
| The LIVE badge does not mean live | See D-01 | Either rename the state, or derive freshness from `timestamp` rather than from which code path produced the value |

---

## Conventions

Derived from reading the code, not from any style guide. Where the code is inconsistent,
the dominant form is named and the deviations are listed.

### Naming

| Thing | Convention | Examples |
|-------|-----------|----------|
| HTML files | lowercase, single word, no separators | `index.html`, `strategy.html`, `custom.html` |
| JS files | lowercase, single word, named for the namespace they export | `vix.js`, `strategy.js`, `chart.js`, `custom.js` |
| Directories | lowercase, single word | `assets`, `css`, `js`, `img`, `data`, `docs` |
| Documentation files | SCREAMING CASE with a `.md` extension | `PRD.md`, `DESIGN.md`, `PATCHNOTES.md`, `README.md` |
| Global namespaces | `Vix` plus PascalCase | `VixData`, `VixStrategy`, `VixChart`, `VixCustom` |
| Functions | camelCase, verb first | `getTier`, `getAllocation`, `saveToCache`, `updateAllocationTable`, `paintCached` |
| Module-level constants | SCREAMING_SNAKE_CASE | `RAW`, `NORMALIZED`, `TIER_LABELS`, `TICKERS`, `ALL_TIERS`, `CATEGORIES`, `CACHE_KEY`, `REFRESH_TTL`, `URLS`, `CONFIG_KEY`, `TICKER_PATTERN`, `SHORT_LABELS`, `TIER_CLASSES` |
| Local variables | camelCase | `vixValue`, `chartInstance`, `loadingTimer`, `cached` |
| Injected globals | double-underscore-wrapped SCREAMING case | `window.__VIX_DATA__`, the only one |
| CSS classes | kebab-case, block-prefixed by area | `.vix-feed-inner`, `.tier-banner`, `.customize-field-desc`, `.chart-legend` |
| CSS modifier classes | the base class plus a hyphenated suffix, applied alongside the base | `.badge-live`, `.btn-large`, `.vix-status-error` |
| CSS custom properties | kebab-case, grouped by prefix | `--bg-*`, `--text-*`, `--accent-*`, `--color-*`, `--tier*-color`, `--sp-*`, `--r-*`, `--font-*` |
| Element ids | kebab-case, used only as JS handles | `vix-value`, `tier-banner`, `allocation-tbody`, `ticker-RISK_OFF` |
| localStorage keys | snake_case, `vix_` prefixed | `vix_last_known`, `vix_custom_tickers` |
| Tier keys | `tier` plus a digit, lowercase | `tier1` through `tier5` |

One deviation: element ids generated for the custom form are `ticker-${c.key}`, which
produces `ticker-RISK_OFF`, mixing kebab-case and SCREAMING_SNAKE_CASE. It is the only
place in the codebase where that happens, and it is a direct consequence of using the
config key as the id. Harmless, but do not treat it as the pattern.

### Formatting

| Aspect | Convention | Notes |
|--------|-----------|-------|
| Indentation | 2 spaces, everywhere, in every file type | No tabs anywhere |
| Quotes in JS | Single quotes | Double quotes appear only inside `data/vix.js`, which is generated as JSON-shaped output by the workflow |
| Quotes in HTML attributes | Double quotes | Universal |
| Semicolons in JS | Always | No exceptions |
| Line length | Roughly 80 to 100 characters, soft | Comment blocks wrap near 72. Long table rows in HTML and long CSS selectors exceed it freely |
| Trailing commas | Present in multi-line object and array literals | Consistent in `strategy.js`, `custom.js`, `chart.js` |
| Alignment | Assignments in destructuring blocks and small maps are column-aligned with extra spaces | See the inline boot scripts and `setStatus`'s map. This is deliberate, matches the terminal aesthetic, and should be preserved when editing those blocks |
| Import ordering | Not applicable. There are no imports | Script tag order in HTML is dependency order, and is load-bearing |
| Blank lines | One between logical blocks, two before a banner comment | |
| CSS rule order | Grouped by area under a banner comment, media queries collected at the bottom of the file | |
| YAML | 2-space indent, comments above the line they describe | |
| Markdown | ATX headings, `---` horizontal rules between major sections, tables for anything enumerable, fenced code blocks with a language tag | |

### Organization

- **File size norm:** JavaScript files run 80 to 115 lines. The largest is `vix.js` at
  113. When a file approaches roughly 150 lines, that is the signal to split it.
- **Logic is split out when two pages need it.** `chart.js` exists because two pages draw
  charts. `custom.js` exists because one page needs categories. The inline boot scripts
  are per-page and are allowed to duplicate each other, which is currently the largest
  source of duplication in the codebase and is recorded as debt.
- **Every library file ends with a single `window.X = { ... }` assignment** listing its
  public surface. That line is the file's export statement and its documentation. Adding
  a function without adding it to that object makes it private by convention.
- **Inline boot scripts are always wrapped in an IIFE**, always sit at the end of
  `<body>`, and always begin by destructuring from the `window.*` namespaces. They never
  wait for `DOMContentLoaded`, because the DOM is already parsed at that point; the
  comment in `strategy.html` explains this and should stay.
- **HTML sections are separated by a full-width banner comment** naming the section.
- **CSS is one file** with the same banner-comment convention.

### Comments

Comment density is low but the comments that exist are unusually substantial. The house
style is that a comment earns its place by explaining *why*, especially when the code
looks wrong or over-complicated without it. There are essentially no comments restating
what a line does.

Concretely, comments in this codebase exist for exactly these reasons:

1. **A non-obvious constraint forced the design.** The IIFE comments in all three inline
   boot scripts, which explain the global scope collision; the header comment in `vix.js`
   explaining why the data file is read as a global rather than fetched; the note that
   `corsproxy.io` returns 403.
2. **A tradeoff was accepted deliberately.** The DST comment in `update-vix.yml`.
3. **Something is intentionally not what it appears to be.** The header comment in
   `custom.js` stating that `sanitizeTicker()` is hygiene and not validation.
4. **A section marker.** The banner comments in the HTML and CSS.

Format: `//` line comments in JS, block comments only for the file headers, `<!-- -->`
banner blocks in HTML, `/* === */` banner blocks in CSS, `#` above the line in YAML.
Sentence case, full sentences, wrapped near 72 characters.

If you are adding a comment that a reader could have derived from the code, delete it. If
you are removing a comment, check first whether it is one of the four types above,
because every one of them is load-bearing.

### Error Handling

The dominant pattern is **return a value, never throw across a module boundary**.

- Every exported function in `vix.js` and `custom.js` returns a well-formed object or
  `null` in every case, including failure. `fetchVIX()` never rejects.
- `try/catch` with an ignored binding, written `catch (_) {}`, is used for every
  `localStorage` and `JSON.parse` operation. Storage can throw in private browsing mode
  and on quota exhaustion, and neither is worth surfacing to the user.
- Internal helpers may throw. `parseResponse()` throws on an unexpected response shape
  and `fetchFromURL()` throws on a non-OK status; both are called inside a `try` in the
  fetch loop.
- Fallback chains are ordered arrays iterated with `for...of` and a `try/catch` per
  iteration, so one bad URL does not stop the next from being tried.
- DOM lookups are always null-checked (`if (!el) return;`) or use optional chaining
  (`document.getElementById('refresh-btn')?.addEventListener`). No code assumes an
  element exists.

Two deliberate exceptions where a throw is allowed to escape: `initChart()` if
`window.Chart` is undefined, and `document.getElementById(canvasId).getContext('2d')` if
the canvas is missing. Both are recorded as debt.

### Logging

There is effectively no logging. The codebase contains no `console.log`, no
`console.warn`, and no `console.error`. Failures are communicated through the UI, via the
status badge and the error text, on the reasoning that a static site with no operator has
no one to read a log. Diagnosis happens through the DevTools network panel and the
runbook's error table.

If you add logging, keep it out of the success path.

### Validation

- **User input** is validated in exactly one place, `sanitizeTicker()` in `custom.js`. It
  is allowlist-based rather than blocklist-based: strip everything outside `[A-Z0-9.-]`,
  cap the length, then test the result against `TICKER_PATTERN` and fall back to the
  default if it fails. Sanitize on write **and** on read, which is what makes a
  hand-edited `localStorage` value safe.
- **External data** is validated by shape, not by schema. `parseResponse()` checks that
  the exact fields it needs exist and are non-null, and throws otherwise.
  `getDataFileVIX()` checks `typeof data.value === 'number'` before trusting the global.
- **Nothing is validated for business plausibility.** A VIX of 900 would render. A ticker
  of `Z.Z-9` would be accepted. Both are deliberate: the first cannot be distinguished
  from a real market event, and the second is deferred to v2.3.0.

### Commit Messages and Branching

Read from the git history, which is the authoritative record here.

**Format:** `type: lowercase imperative description`, optionally with a trailing
`(vX.Y.Z)`. Roughly 90 percent of the history follows it.

Types actually in use, by frequency: `feat`, `docs`, `fix`, `chore`, `config`.

Observed examples:

```
feat: add Custom strategy builder (v1.2.0)
docs: reprioritize post-MVP roadmap
fix: wrap inline boot scripts in IIFEs to fix global scope collision
chore: update VIX data [skip ci]
config: increase cache TTL from 60 seconds to 30 minutes (v1.0.6)
```

Deviations, all early in the history: `Fix: dashboard never booted on GitHub Pages
(v1.0.3)` uses a capitalized type, and `Initial release: VIX Strategy v1.0.2` does not
use a type at all. Match the dominant lowercase form.

`chore: update VIX data [skip ci]` is generated by the workflow and must keep the
`[skip ci]` marker.

**Branching:** trunk-based, and strictly so. There is one branch, `main`. There are no
feature branches in the history, no merge commits other than two
`Merge branch 'main' of https://github.com/Azqato/vix` entries from pulling before a
push, no pull requests, and no tags. Every commit goes straight to `main`, which is also
the live site. This is viable only because there is a single maintainer and the deploy is
instant and instantly revertible.

If a change is large enough that you would not want it live for the few minutes before
you notice a problem, use a branch. Nothing in the tooling stops you.

---

## Writing Style

The project had no stated prose rule before v1.2.1. The following is adopted as the
default and applies to documentation, UI copy, and code comments.

- **Em dashes are prohibited in all three forms:** the literal Unicode character, the
  `&mdash;` HTML entity, and the double dash used as punctuation. The Unicode character
  and the entity must be searched independently, because a search for one does not catch
  the other. CSS custom properties (`--color-bg` and the like) are valid syntax, not
  punctuation, and are never touched. Neither are CLI flags (`--oneline`), HTML comment
  delimiters, or the `--` placeholder string the UI uses for an unknown VIX value.
- **Replace each instance** with whichever alternative fits: a comma (most natural in
  most cases), a colon (introducing a list or an elaboration after a complete clause), a
  semicolon (joining two closely related independent clauses), parentheses (asides and
  supplementary detail), a period (splitting one sentence into two), or a single hyphen.
- **The single hyphen is permitted and encouraged** where context justifies it. The
  prohibition does not cover it, and it is the closest visual match, so prefer it in
  document titles, section headings, and version lines (for example `## v1.2.0 -
  2026-01-01`) where a comma or colon reads awkwardly. In running prose the other
  replacements are usually better.
- **Leave any instance the text needs in order to mean anything**, such as a rule, a
  table, or an example naming the character it prohibits. Replacing those destroys the
  line.
- **Tone:** direct and functional, plain declarative sentences, no marketing language, no
  filler openings.

**Current compliance.** The three documents rewritten in v1.2.1 (`README.md`, this file,
`DESIGN.md`) and the rewritten `PATCHNOTES.md` are compliant. The rest of the project is
not: 274 literal em dashes remain in the source files, none as `&mdash;` entities and
none as double dashes. The full inventory is in the v1.2.1 patch notes entry. They were
not changed during this audit because that would mean editing shipped UI copy and code
comments, which is a product change rather than a documentation change. Cleaning them up
is logged as open question 7.

The en dash (the character between "1" and "3" in "1-3 Month" as Yahoo writes it) is not
covered by this rule and appears in several ticker names copied verbatim from the fund
issuers. Leave those alone.

---

## Browser Testing

The project had no stated rule for this before v1.2.1, and it has no automated tests at
all. The following is adopted as the default so that the rule exists before the first
test does.

- **Use Microsoft Edge, never Chrome.** Where there is no JavaScript runtime on the
  maintenance machine, end-to-end testing is done by driving a headless browser directly,
  and Chrome is the owner's day-to-day browser. Driving it would disturb a live session.
  Edge runs the same engine and is free to use.
- **This applies to every browser a test drives**, not only one named in a config file.
  An ad hoc headless invocation from a script or a shell command is testing and falls
  under the same rule.
- **Resolved browser binary path**, which differs by platform and is the first thing that
  breaks on a new machine:

| Platform | Path |
|----------|------|
| Windows (the current maintenance machine) | `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe` |
| Windows, per-user install | `%LOCALAPPDATA%\Microsoft\Edge\Application\msedge.exe` |
| macOS | `/Applications/Microsoft Edge.app/Contents/MacOS/Microsoft Edge` |
| Linux | `/usr/bin/microsoft-edge` or `/opt/microsoft/msedge/msedge` |

  Verify the path before relying on it. It was not executed during this audit, because
  the audit was read-only, so treat the Windows entry as the expected default location
  rather than a confirmed fact on this machine.

- **A second engine is not currently targeted.** The site uses no engine-specific APIs
  beyond `-webkit-background-clip`, which has a standard fallback in place, and
  `backdrop-filter`, which degrades to the opaque header background. Manual spot checks
  in Firefox and Safari are worthwhile before a release that touches layout, but they are
  not a gate, and nothing in the project currently automates them.

Manual browser verification steps are in [Working Practice](#working-practice).

---

## Security

### Authentication Model

None. VIX Strategy has no user authentication, no login flow, no sessions, and no user
accounts. All pages are publicly accessible to anyone with a browser. There is no admin
interface and no privileged access. Nothing in the application distinguishes one visitor
from another.

### Authorization Model

None. There are no user roles and no access-controlled resources. Every visitor sees the
same pages and the same data. The only privileged actor anywhere in the system is the
`update-vix.yml` GitHub Actions workflow, which holds `contents: write` on the repository
so it can commit `data/vix.js`. That permission is scoped to the repository and to the
duration of the workflow run.

### Data Storage

| Data | Location | Contents | Accessible by | Protection |
|------|----------|----------|---------------|-----------|
| VIX cache | `localStorage['vix_last_known']`, in the browser | Last VIX value, ISO timestamp, `fetchedAt` | JavaScript on the same origin only | Browser same-origin policy |
| Custom strategy tickers | `localStorage['vix_custom_tickers']`, in the browser | Four ticker strings, sanitized to `[A-Z0-9.-]`, max 10 characters each | JavaScript on the same origin only | Same-origin policy, plus sanitization on both write and read |
| VIX data file | `data/vix.js` in the repository, served publicly | Last VIX value, ISO timestamp, `fetchedAt` | Publicly readable. Writable only by the workflow via `GITHUB_TOKEN` | Only one write path exists, and no user input reaches it |

**No user data of any kind is collected, stored server-side, or transmitted.**
`localStorage` holds a market number, a timestamp, and up to four ticker symbols the user
typed. It contains zero personally identifying information. Nothing in it ever leaves the
browser.

### Environment Variables

None. There is no backend, no build step, and no secret of any kind. There is no `.env`
file, no API key, no database credential, and no configuration value that must be
protected. **Nothing that resembles a secret should ever be committed to this
repository.**

The Yahoo Finance endpoint and the allorigins.win proxy URL are public, unauthenticated,
and intentionally visible in source. `GITHUB_TOKEN` is injected by GitHub Actions at run
time and is never declared or stored.

### Third-Party Trust

Every outbound request the application makes, and what each recipient receives:

| Service | URL | Data transmitted | Purpose |
|---------|-----|------------------|---------|
| Same origin | `/data/vix.js` on `azqato.github.io` | Nothing beyond a normal HTTP request. Loaded as a script, not fetched | Primary VIX data source |
| GitHub Pages | The site itself | Browser IP and user agent, as with any web request | Serving the site |
| jsDelivr CDN | `https://cdn.jsdelivr.net/npm/chart.js@4.4.0/...` | Browser IP and user agent | Serving Chart.js |
| allorigins.win | `https://api.allorigins.win/raw?url=...` | The target URL, plus browser IP and user agent | Fallback VIX fetch only |
| Yahoo Finance | `https://query1.finance.yahoo.com/...` | Via the relay: allorigins.win's server IP. Via the workflow: the GitHub runner's IP. The browser's own IP only if the relay forwards it | Source of VIX market data |

**No personal data, financial data, portfolio information, or identifying information is
transmitted to any party.** The tickers a user enters on `custom.html` are never sent
anywhere; they are written to that browser's `localStorage` and read back by the same
page. The only data leaving the browser is the standard HTTP metadata inherent in any web
request.

### Known Attack Surface

#### Custom ticker input

`custom.html` is the only page that accepts free-text user input, added in v1.2.0.
Unsanitized user input is the most common source of XSS in web applications, so this is
the highest-value surface in the app despite being small.

**Mitigation, two independent layers:**

1. **Input sanitization.** `sanitizeTicker()` uppercases, strips everything outside
   `[A-Z0-9.-]`, caps at 10 characters, and tests the result against `TICKER_PATTERN`,
   falling back to the category default on failure. This alone eliminates `<`, `>`, `"`,
   `'`, `&`, and every other HTML-meaningful character. It runs on write and on read, so
   a hand-edited `localStorage` value is sanitized before it reaches the DOM.
2. **Safe rendering.** Every render path for a user-entered value uses
   `document.createElement()` with `.textContent` or `.value`, never `innerHTML` string
   concatenation. This holds for the form labels, the chart legend, the allocation table,
   and the Chart.js label array. So even if the sanitizer were loosened or bypassed, no
   sequence of characters could execute as markup.

**Known limitation:** the ticker is not verified against a real quote, deferred to
v2.3.0. A user can enter any string matching the allowlist. That is a data-quality gap,
not a security one, since the render path is injection-safe regardless of whether the
symbol means anything.

#### CORS proxy response injection

**Risk:** a compromised allorigins.win could return malicious JSON instead of valid VIX
data.

**Mitigation:** `parseResponse()` reads exactly two fields, coercing one to a number and
the other to a Date, and throws if either is missing. No content from the proxy response
reaches `eval()`, `innerHTML`, or `Function()`. A malformed response falls through to the
cached value.

**Residual risk:** the proxy could return a plausible but wrong VIX number, which would
produce a wrong allocation with no visible sign of tampering. There is no mitigation for
this other than the fact that the proxy is a fallback path that should almost never run.

#### DOM injection via rendered data

**Risk:** if VIX values, ETF names, or user tickers were inserted unsanitized they could
carry an XSS payload.

**Mitigation:** VIX values are numbers rendered with `.textContent`. ETF names and
descriptions are hardcoded constants in `strategy.js`, not derived from any response. The
template literals that build table rows and legend items on `index.html` and
`strategy.html` interpolate only those constants and numeric percentages. `custom.html`
uses no template literals at all in any path touching user input.

#### `data/vix.js` as an executable script

**Risk:** unlike a JSON file, `data/vix.js` is loaded via `<script src>` and executed with
full page privileges. Anything landing in that file runs.

**Mitigation:** the only write path is the `update-vix.yml` workflow, which generates the
file from a fixed template with two values interpolated from Yahoo Finance's own numeric
response fields. No user input can reach it. The operative controls are the repository's
write access and `GITHUB_TOKEN` scoping, which is the same trust boundary as every other
file on `main`.

**Uncertain:** whether branch protection is enabled on `main` was not verifiable from the
local clone during this audit. If it is not, anyone with push access can write arbitrary
JavaScript into a file that every visitor executes. Logged as open question 8.

#### CDN compromise (Chart.js)

**Risk:** a compromised jsDelivr could serve a modified Chart.js bundle that executes
arbitrary JavaScript in every visitor's browser on two of the three pages.

**Mitigation, current:** the version is pinned to 4.4.0, which prevents a silent upgrade
but does not prevent a targeted replacement of that version's files.

**Known debt:** no Subresource Integrity hash is present. Adding
`integrity="sha384-..."` and `crossorigin="anonymous"` would make the browser reject any
response that does not match the expected hash. This is the single highest-value security
improvement available to the project and it is a one-line change.

#### Surfaces that do not exist

There is no SQL injection surface, because there is no database. No CSRF surface, because
there is no authenticated state and no server-side mutation to forge. No SSRF surface,
because no server of ours makes a request on a user's behalf. No session fixation, no
privilege escalation, and no password handling, because there are no accounts.
`index.html` and `strategy.html` have no forms, no search fields, and no query parameters
that affect rendering.

### Dependency Policy

- **Runtime dependencies:** one, Chart.js 4.4.0, delivered from a CDN.
- **Development dependencies:** none. No `package.json`, no lock file, no npm.
- **Version pinning:** Chart.js is pinned directly in the `<script src>` URL, so any
  update requires a deliberate code change and appears in the diff.
- **Vulnerability scanning:** not automated. There is no `package.json` for Dependabot to
  scan, so GitHub security alerts do not apply. This is a real gap, mitigated only by the
  dependency count being one.
- **Update policy:** Chart.js is updated manually when a vulnerability is publicly
  disclosed for the pinned version. Check
  `https://github.com/chartjs/Chart.js/security/advisories` periodically. Add this to the
  monthly review in the [Metrics](#metrics) reporting cadence.
- **GitHub Actions dependencies:** `actions/checkout` is pinned to a major version tag
  (`v7`), which means patch and minor updates are accepted automatically. That is the
  normal tradeoff for a first-party action.

---

## Deprecation and Removal

### The Project's Existing Rule

The project has never written down a removal policy, but the changelog and the code show
a consistent practice: **removals and moves are plain, with no compatibility shim left
behind.** The clearest precedent is v1.0.11, which moved every documentation file from
the repository root into `/docs` and left no stub, redirect, or forwarding file at any of
the old paths. The v1.1.0 conversion from ES modules to classic scripts removed every
`export` statement outright rather than keeping a deprecated re-export. In both cases the
change was recorded in the patch notes and nothing else was preserved.

That existing practice is documented here and left alone. It matches the default policy
below for internal files, so there is no conflict to flag.

### The Policy

Whether a removal needs a redirect is decided by whether the thing being removed is
public facing, not by the fact that it is being removed.

- **Public facing:** the deployed artifact and the addresses it serves. Removing one
  retires the address behind a redirect, alias, or equivalent compatibility shim pointing
  at whatever replaces it, so the old address keeps resolving.
- **Internal:** the source that builds the artifact, and anything else not reachable from
  outside. Source files are not public facing even when their names appear in a built URL,
  because the name is derived from the source rather than being the contract. Removing one
  is a plain delete: no redirect, no alias, no stub file, no tombstone. Nothing external
  points at it, so there is no address to preserve, and a permanent compatibility entry
  would be maintenance in exchange for nothing.

### Where This Project Draws the Deploy Boundary

This project has an unusual property that has to be stated plainly, because the rule
cannot be applied without it: **GitHub Pages serves the entire repository root.** There is
no build output directory and no publish filter. Every file in `main`, including the
markdown in `/docs` and the workflow YAML, is fetchable as a URL. Taken literally, that
would make every file public facing.

The project draws the line by **contract, not by reachability**:

- **Public facing** is the set of addresses the site or its documentation actually points
  a reader at. Concretely: the three HTML pages, the stylesheet, the four JavaScript
  files, the two SVGs, and `data/vix.js`. These are referenced by `<a>`, `<link>`,
  `<script src>`, `<img>`, or by the README, so an external link or a bookmark can exist
  for each.
- **Internal** is everything else that Pages happens to also serve: the contents of
  `/docs`, `.github/workflows/`, and `.claude/`. These are repository source. They are
  readable over HTTP as an artifact of the hosting model, not as an offer.

The reasoning, recorded so it is not relitigated: treating incidental HTTP reachability as
a contract would freeze the documentation file layout permanently, which is the opposite
of what documentation is for. The documents are read on GitHub, in an editor, or through
the links in the README, all of which follow the repository rather than a URL.

### Redirect Mechanism

**The project has no redirect mechanism, and cannot have one.** GitHub Pages serving a
plain branch supports no rewrite rules, no `_redirects` file, and no server configuration.
The only available substitutes are an HTML file at the old path containing a
`<meta http-equiv="refresh">`, or a JavaScript redirect. Neither exists in the repository
today.

What the project does instead: it keeps a **Retired Items** table, below, so that any
reference to something that no longer exists can be resolved by reading this document.
For an HTML page, if one is ever removed, the policy is to leave a meta-refresh stub at
the old path pointing at the replacement, since that is the only mechanism the platform
allows and an HTML page is unambiguously public facing.

### Public Surface, Item by Item

This is the list the policy is applied against. If a file is on it, removing it retires an
address. If it is not, removing it is a plain delete.

| Address | Type | Referenced by |
|---------|------|---------------|
| `/` and `/index.html` | HTML page | The nav on all three pages, the footer CTA on `strategy.html`, the README, the PRFAQ press release, external links |
| `/strategy.html` | HTML page | The nav on all three pages, two CTAs on `index.html`, the footer CTA on `custom.html` |
| `/custom.html` | HTML page | The nav on all three pages |
| `/assets/css/styles.css` | Stylesheet | `<link>` on all three pages |
| `/assets/js/vix.js` | Script | `<script src>` on all three pages |
| `/assets/js/strategy.js` | Script | `<script src>` on all three pages |
| `/assets/js/chart.js` | Script | `<script src>` on `strategy.html` and `custom.html` |
| `/assets/js/custom.js` | Script | `<script src>` on `custom.html` |
| `/assets/img/logo.svg` | Image | `<img>` in the nav on all three pages, and the Open Graph image on `index.html` |
| `/assets/img/favicon.svg` | Image | `<link rel="icon">` on all three pages |
| `/data/vix.js` | Data script | `<script src>` on all three pages, and named in the runbook's monitoring checks as a URL to open |

Everything not on this list, specifically `/docs/*`, `/.github/**`, `/.claude/**`, and
`/README.md`, is internal under the rule above.

`README.md` is an edge case worth naming: it is public in the sense that GitHub renders
it prominently, but it is not an address anything links to by URL, and GitHub resolves it
by convention rather than by path. Its rename from `README.MD` to `README.md` in v1.2.1
was therefore treated as an internal rename and no stub was left.

### Compatibility Entries

The project currently has **zero** compatibility entries: no redirects, no aliases, no
deprecated re-exports, no stub files. If one is ever added, these rules apply:

- It is **permanent**. A compatibility entry is never removed on the grounds that "enough
  time has passed", because there is no way to observe whether anything still uses it.
- It is **never chained**. A redirect always resolves to a real target in one hop. Two
  hops become three, and eventually one of them rots.
- It is **never reused** to point at different content later. A reused address silently
  serves the wrong thing, which is worse than a broken link, because a broken link tells
  the reader something is wrong and a wrong page does not.

### Retired Items

| Item | Removed | Version | Replaced by |
|------|---------|---------|-------------|
| `docs/TRD.md` | 2026-08-25 | v1.2.1 | [Technical Requirements](#technical-requirements) in this file |
| `docs/ROADMAP.md` | 2026-08-25 | v1.2.1 | [Roadmap](#roadmap) in this file |
| `docs/METRICS.md` | 2026-08-25 | v1.2.1 | [Metrics](#metrics) in this file |
| `docs/TENETS.md` | 2026-08-25 | v1.2.1 | [Tenets](#tenets) in this file |
| `docs/RUNBOOK.md` | 2026-08-25 | v1.2.1 | [Runbook](#runbook) in this file |
| `docs/SECURITY.md` | 2026-08-25 | v1.2.1 | [Security](#security) in this file |
| `docs/PRFAQ.md` | 2026-08-25 | v1.2.1 | [Press Release](#press-release) and [Frequently Asked Questions](#frequently-asked-questions) in this file |
| `README.MD` (uppercase extension) | 2026-08-25 | v1.2.1 | `README.md`, same location, rewritten for a general reader. Its developer content moved to the [Runbook](#runbook) |
| Root-level `PRD.MD`, `README.MD`, `PATCHNOTES.MD` | 2026-06-08 | v1.0.11 | The `/docs` versions. Recorded here because the v1.0.11 patch notes are the only other trace |
| `corsproxy.io` as the CORS proxy | 2026-06-06 | v1.0.2 | `api.allorigins.win`. Yahoo Finance was returning HTTP 403 to corsproxy.io's servers |
| `sessionStorage` caching | 2026-06-06 | v1.0.4 | `localStorage`, so the value survives a tab close and is shared across pages |
| ES module syntax (`type="module"`, `import`, `export`) | 2026-07-09 | v1.1.0 | Classic scripts with `window.*` namespaces, required for `file://` support |
| The hardcoded `18.4` VIX placeholder on `index.html` | 2026-06-06 | v1.0.4 | The live hero gauge wired to the same cache as the dashboard |
| `DOMContentLoaded` listener on `strategy.html` | 2026-06-06 | v1.0.3 | A direct async IIFE, since module scripts are deferred by spec and the event had already fired |

Historical records are not rewritten when something is removed. Patch notes entries and
milestone rows describing a deleted item stay exactly as they are, because they record
what happened at the time rather than describing the current state.

---

## Documentation Versus Reality

Every discrepancy found during the 2026-08-25 audit. The code is treated as the truth
about what is; the documentation is treated as the truth about what was intended. Resolved
entries are kept with a note on how they were resolved, so the record shows what was found
and what was decided.

| ID | Discrepancy | Trust | Resolution |
|----|-------------|-------|------------|
| D-01 | The UI shows a `LIVE` badge whenever the value came from `window.__VIX_DATA__` or the proxy on this page load, regardless of how old the quote is. `data/vix.js` currently holds a value quoted 2026-07-09, so production shows `LIVE` next to a value roughly seven weeks old. Every document described `LIVE` as meaning fresh data. | Code. The badge logic in `strategy.html` and `custom.html` sets `fromCache: false` for the data-file path, which maps to `LIVE`. | Documented as-is throughout: the badge means "read this page load", not "quoted recently". The timestamp text beside it is the honest freshness signal. Fixing the badge is a code change and is open question 1's dependent. |
| D-02 | PRD said "strategy.html refreshes VIX every 60 seconds automatically". The 60-second interval exists, but `fetchVIX()` returns the cached value untouched when it is under 30 minutes old, so the displayed value can change at most every 30 minutes. | Code. Both the interval and the TTL are unambiguous. | Both facts documented together in the [Feature List](#feature-list). The original sentence's intent (the page updates itself without user action) is true; its implied frequency is not. |
| D-03 | PRD, RUNBOOK, and the PRFAQ all described the Refresh button as forcing a "live fetch". Since v1.1.0 it clears the cache and re-runs `fetchVIX()`, which reads the committed `data/vix.js` first. The network is only touched if that global is missing. | Code. | Reworded everywhere to "clears the cache and re-runs the fetch path". The user-facing consequence, that Refresh cannot produce a fresher number than the last workflow run, is stated in the runbook's error table and in the external FAQ. |
| D-04 | PRD Success Criteria required the risk disclaimer to be "visible without scrolling" on `index.html` and `strategy.html`. It is below the fold on all three pages, and on `strategy.html` the main disclaimer is inside a collapsed accordion. | Code, for what is. The document, for what was intended. | Neither text was deleted. The criterion is preserved verbatim in [Success Criteria](#success-criteria) with the observed reality beside it and marked unmet. Resolving it is a product decision, open question 3. |
| D-05 | The old TRD's architecture diagram and its statement that "all four scripts" load in order predated `custom.html`. It listed only `index.html` and `strategy.html` and omitted `custom.js` from the browser column. | Code. `custom.html` shipped in v1.2.0 and loads five scripts. | The diagram in [Technical Requirements](#technical-requirements) was redrawn to include all three pages and all five scripts. |
| D-06 | The old TRD's folder tree listed `README.md`. The actual file was `README.MD`. | Code, at the time. | Resolved by renaming the file to `README.md` in v1.2.1, which makes the document's original claim true. |
| D-07 | The old TRD targeted a cached-display time of under 100ms; the old METRICS targeted under 200ms. Two documents, two numbers, neither measured. | Neither. Both were aspirational. | Consolidated to a single 200ms target in [Metrics](#metrics), with the contradiction noted in place. |
| D-08 | Commit `c0d0477`, "feat: add Azqato Invests link to navigation", shipped a user-visible nav change with no patch notes entry. Commit `028582a`, "docs: update repo/URL references to lowercase vix", is likewise unrecorded. Both landed between v1.0.12 and v1.1.0. | Code and git history. The links are live on all three pages. | Recorded in the [Milestone Table](#milestone-table) as unversioned, and added to `PATCHNOTES.md` as an "Unreleased, retroactively recorded" entry rather than being back-dated into an existing version, since inventing a version number would misrepresent the history. |
| D-09 | DESIGN.md documented three contrast ratios (18.9:1, 11.5:1, 5.7:1) that do not match a recomputation of the same color pairs (19.3:1, 14.4:1, 7.6:1). | The recomputation, which is reproducible from the WCAG formula. | Both figures are in the DESIGN.md table, the original in a Note column. All three pass either way, so nothing was at stake. |
| D-10 | DESIGN.md said the missing `prefers-reduced-motion` support "should be addressed before a v1.1 release". v1.1.0, v1.1.1, and v1.2.0 have all shipped without it. | Code. The stylesheet contains zero occurrences. | Documented as an unmet deadline rather than silently updated, so the record shows the debt was deferred three times. |
| D-11 | DESIGN.md's type scale listed "H4, 0.78rem, mono", which describes `.accordion-content h4`. The bare `h4` element rule is `1rem` in the body font. | Code. Both rules exist. | Split into two rows in the DESIGN.md type scale, with a note that only the accordion variant actually appears in the markup. |
| D-12 | No document mentioned that the five-tier allocation table on `index.html` is hardcoded HTML rather than rendered from `strategy.js`. Editing the strategy would silently leave the pitch page wrong. | Code. | Added to [Known Technical Debt](#known-technical-debt) and to the [Working Practice](#working-practice) checklist as a required step when changing allocations. |
| D-13 | No document mentioned that `custom.html` renders the tier banner in a different format from `strategy.html`, or that it never calls `getTierLabel()`. | Code. | Documented in DESIGN.md's Tier Banner section and in [Known Technical Debt](#known-technical-debt). |
| D-14 | No document described the scanline overlay (`body::before`), the hero radial glow, the hero gradient text, or the Chart.js tooltip and center-plugin styling, all of which are significant to the visual identity. | Code. | Added to DESIGN.md under Page Chrome, Chart, and the hardcoded-colors table. |
| D-15 | No document listed the colors hardcoded outside the token system, in `chart.js` and in several CSS rules. | Code. | Added as a table in DESIGN.md. |
| D-16 | The old SECURITY.md asserted that "repository branch protection" is an operative control on `data/vix.js`. Whether branch protection is actually configured is not visible from a local clone. | Neither. It is unverified. | Restated in [Security](#security) as uncertain, and raised as open question 8 rather than repeated as fact. |
| D-17 | The old PRFAQ internal FAQ answer 7 described the codebase as "open source". The repository contains no `LICENSE` file, so the default is all rights reserved. | Code. The absence is verifiable. | Flagged in [Folder Structure](#folder-structure) and raised as open question 2. The FAQ answer was reworded to say the source is public and readable, which is true, and to note the missing license. |
| D-18 | `docs/PRFAQ.md` was dated 2026-06-08 and its press release describes a June 8 launch, but it was edited after v1.2.0 to add a Custom-builder FAQ, leaving a document that is simultaneously a launch-day artifact and a current one. | The document, for the launch narrative. The code, for the feature list. | The press release in this file keeps its original 2026-06-08 dateline, since a press release records a moment. The FAQs were updated to current state and are dated separately. |
| D-19 | The old README was written for developers and led with a tech stack table, install steps, and deploy commands. The required standard is that the README is for a general reader and carries no commands. | The standard. | `README.md` fully rewritten. Every command, version, and technical detail it carried now lives in the [Runbook](#runbook) and [Technical Requirements](#technical-requirements) sections here, so nothing was lost. |
| D-20 | No document stated a prose style rule, a browser-testing rule, or a removal policy. | Nothing existed to contradict. | Defaults adopted and written in as [Writing Style](#writing-style), [Browser Testing](#browser-testing), and [Deprecation and Removal](#deprecation-and-removal). The removal section documents the project's existing practice, which already matched the default. |
| D-21 | `data/vix.js` has not been updated since 2026-07-09, 47 days before this audit, despite a schedule that should produce up to 8 runs per weekday. No document acknowledged that the pipeline might be dormant. | Code and git history, as far as the local clone can see. | Raised as open question 1 and added to the weekly monitoring check as the single most important recurring item. Not resolved: the Actions tab was not accessible during a read-only local audit. |
| D-22 | The old TENETS.md and the old SECURITY.md both described `index.html` and `strategy.html` as having no user input, which was written before `custom.html`. The security document was updated for v1.2.0; the tenets document was not, but its claim was general enough to remain true. | Code. | Both restated accurately here. |

---

## Risks and Open Questions

This section is deliberately the least confident part of the document, and is worth more
than the confident parts.

### What Was Not Fully Understood

- **The live state of the GitHub Actions workflow.** The audit was read-only and local.
  The Actions run history, whether the schedule is currently enabled, whether recent runs
  succeeded or failed, and whether GitHub has auto-disabled it, were all unobservable. The
  only evidence available is that `data/vix.js` has not changed in the local clone or on
  `origin/main` since 2026-07-09. That could mean the workflow is broken, disabled, or
  simply that the local clone has not fetched. This is the largest gap in the audit.
- **Whether branch protection is configured on `main`.** Not visible from a clone. It
  matters because `data/vix.js` executes as JavaScript on every page load.
- **Actual production behavior.** Nothing was loaded in a browser during this audit. Every
  statement about rendering, timing, and console cleanliness is derived from reading the
  code, not from observing it. Where a claim was carried forward from a previous document
  and could not be verified by reading, it is marked as such.
- **The contrast ratios** were computed by hand from the WCAG relative luminance formula
  rather than measured with a tool. The arithmetic is straightforward and reproducible,
  but a tool would be more trustworthy, particularly for the three rows that sit within
  0.3 of the 4.5:1 threshold.
- **Whether anyone actually uses the site.** There is no analytics and no feedback
  channel, so every statement about users in this document is a description of an intended
  user, not an observed one.

### Fragile Areas

- **Script load order in the three HTML files.** It is dependency order, it is
  hand-maintained, and it is unenforced. Reordering two tags or adding a new script in the
  wrong position produces a `Cannot read properties of undefined` failure that kills the
  entire boot script. No test would catch it.
- **The IIFE wrappers around the inline boot scripts.** Removing one reintroduces the
  v1.1.1 `SyntaxError`, which fails silently and completely. Each has an explanatory
  comment for this reason. Do not remove either.
- **`data/vix.js` is executed, not parsed.** It is the only file in the project that is
  both machine-generated and executed with full page privileges.
- **The heredoc in `update-vix.yml` that generates `data/vix.js`.** It interpolates
  GitHub Actions expressions directly into JavaScript source. It works because the values
  are numeric, but there is no quoting or escaping step, so a change to what Yahoo returns
  could produce a syntactically broken file that breaks all three pages at once.
- **Zero tests.** `getTier()`, `normalize()`, and `sanitizeTicker()` are pure functions
  with clear contracts and no coverage at all. Every verification in this project is a
  human looking at a page.
- **Duplicated chrome across three HTML files.** The nav, header, footer, and disclaimer
  are copy-pasted. A change made in two of three files produces an inconsistency that
  nothing detects.
- **The hardcoded allocation table on `index.html`.** It duplicates `RAW` from
  `strategy.js` as literal text. Changing the strategy in one place and not the other
  produces a site that contradicts itself.
- **No `TODO`, `FIXME`, or `HACK` markers exist anywhere in the codebase.** This was
  checked explicitly. That is unusual and is a genuinely good sign, but it also means the
  code carries no in-place record of its own rough edges; that record exists only in these
  documents, which is a reason to keep them current.

### Dangerous to Change Without More Context

| Change | What breaks |
|--------|-------------|
| Converting any script back to `type="module"` | The site stops working entirely under `file://`, which is a documented supported environment |
| Fetching `data/vix.js` instead of loading it as a script | Same. `fetch()` of a local file is blocked under `file://` |
| Removing an IIFE from an inline boot script | A `SyntaxError` on page load, silently killing every DOM update on that page |
| Changing the `RAW` tier weights in `strategy.js` alone | `index.html`'s hardcoded table now contradicts the dashboard |
| Removing the allorigins.win fallback from `vix.js` | The site has no recovery path if `data/vix.js` is ever unavailable. The debt entry proposing this removal is conditional on the data-file path being verified stable, which open question 1 currently makes doubtful |
| Adding a `package.json` | Nothing breaks immediately, but it ends the "the repository is the artifact" property and invites a build step, which would break `file://` |
| Changing `sanitizeTicker()`'s allowlist | Directly widens the only user-input attack surface in the app |
| Renaming any file listed in the [Public Surface](#public-surface-item-by-item) table | Breaks an address with no redirect mechanism available to fix it |

### Work in Progress

There is none. At the time of this audit the working tree is clean, `main` and
`origin/main` point at the same commit (`0417378`), there are no other branches, no tags,
no stubbed functions, and no half-finished features in the source. The only unfinished
work is what is listed in the roadmap and in the debt table, and none of it has been
started.

### Open Questions

Numbered so they can be answered by reference. When one is answered, fold the answer into
the relevant section and mark it answered here rather than deleting it.

1. **Is the `update-vix.yml` workflow still running?** `data/vix.js` has not changed since
   2026-07-09, 47 days before this audit. Check the Actions tab. If the schedule was
   auto-disabled for inactivity, any commit re-enables it. If runs are failing, the likely
   cause is a change to the Yahoo Finance response shape, which would need `parseResponse()`
   in `vix.js` and the `jq` filters in the workflow updated together. This blocks any
   confident statement about data freshness and affects D-01, D-21, and the conditional
   debt item about removing the proxy fallback.
2. **Should the repository have a `LICENSE` file?** The FAQ describes the project as open
   source and invites forking, but with no license the legal default is all rights
   reserved, which contradicts the invitation. If forking is intended, add a license. If
   not, the FAQ wording should change.
3. **What should the risk-disclaimer visibility criterion actually say?** The current
   criterion ("visible without scrolling") is not met on any page. Either reword it to
   match the intent, that risk language is prominent, plain, and not dismissible, or add
   an above-the-fold disclaimer to all three pages. This is a product call, and tenet 5
   makes it non-trivial.
4. **Should `--text-muted` be lightened?** It fails WCAG AA against all three backgrounds
   it is used on, and one of the affected elements is the risk disclaimer text. Roughly
   `#8b93a1` would clear 4.5:1 everywhere. The counterargument is that the current value is
   part of the terminal aesthetic. Tenet 5 argues for the change.
5. **Should analytics be added?** Every metric in the [Metrics](#metrics) section is
   currently unmeasurable, which makes the whole section aspirational. A
   privacy-respecting, cookieless tool would make it real, at the cost of the absolute
   no-tracking promise the site currently makes in its own FAQ. If the answer is no, the
   metrics section should be reframed around what GitHub Insights can actually show.
6. **How should the Growth Rocket variant handle its different tier boundaries?** The
   drafted table uses 15/20/30/40 rather than the core strategy's 15/25/35/45, so v2.2.0 is
   not a table swap: `getTier()` would need to become tier-set-aware. The milestone scope
   does not currently reflect that.
7. **Should the 274 em dashes in the source files be replaced?** The
   [Writing Style](#writing-style) rule adopted in v1.2.1 prohibits them, but applying it
   would mean editing shipped UI copy on all three pages plus code comments, which was out
   of scope for a documentation audit. It is a mechanical change with a visible effect on
   the site's prose. Needs a yes or no.
8. **Is branch protection enabled on `main`?** Not verifiable from a clone. It matters
   because `data/vix.js` is executed as JavaScript by every visitor, so write access to
   `main` is effectively script-execution access on the live site.
9. **Should the sparkline stay at v1.3.0?** It was slated as v1.2.1 before this audit
   consumed that number for the documentation release. Renumbering a planned, unshipped
   milestone is low-risk, but it is the author's call.

---

## Working Practice

Concrete instructions for future work, human or model.

### Before Editing Anything

1. `git status`. The tree should be clean. If it is not, understand why before adding to
   it.
2. `git log --oneline -10`. Check whether a `chore: update VIX data` commit landed
   recently. If none has in over a day of trading, that is open question 1 and it may be
   the actual problem you are about to work around.
3. Read the section of this document that covers what you are changing. The table below
   maps work to the file to open.
4. Open the page in a browser before you change it, so you know what "working" looked
   like.

### Where to Look First

| Kind of work | Open first |
|--------------|-----------|
| Changing tier boundaries or allocation percentages | `assets/js/strategy.js` (`RAW`, `getTier`), then the hardcoded table in `index.html`, then [Strategy Logic](#strategy-logic) here |
| Changing how VIX data is fetched, cached, or aged | `assets/js/vix.js`, then [Technical Requirements](#technical-requirements) |
| Changing the scheduled data refresh | `.github/workflows/update-vix.yml`, then the Third-Party Integrations table here |
| Any color, spacing, font, or breakpoint | `docs/DESIGN.md` first, then `assets/css/styles.css` |
| Adding or changing a chart | `assets/js/chart.js`, then the Chart section of `docs/DESIGN.md` |
| Anything touching the Custom page or user input | `assets/js/custom.js`, then [Security](#security) here |
| Adding a page | The nav block in all three existing HTML files, [Deprecation and Removal](#deprecation-and-removal) for the public surface list, and the Navigation Links section of `docs/DESIGN.md` |
| Deploy, rollback, or a production incident | [Runbook](#runbook) |
| Deciding whether a feature belongs at all | [Tenets](#tenets), then [Non-Goals](#non-goals), then [Explicitly Deferred](#explicitly-deferred) |
| Understanding why something looks wrong but was built that way | [Documentation Versus Reality](#documentation-versus-reality), then [Risks and Open Questions](#risks-and-open-questions) |
| Recording what you did | `docs/PATCHNOTES.md` |

### Never Do These

- **Never convert a script to `type="module"`, and never `fetch()` a local file.** Both
  break `file://` support completely and silently. This is the single constraint the
  entire architecture is shaped around.
- **Never remove an IIFE from an inline boot script.** It reintroduces the v1.1.1
  `SyntaxError`, which kills the whole script before the first DOM write, with no visible
  error other than a dead page.
- **Never change the allocation numbers in only one place.** They exist in
  `strategy.js` `RAW` and as literal text in `index.html`.
- **Never render user-entered text with `innerHTML`.** Use `document.createElement()` and
  `.textContent`. The sanitizer is the first layer of defense, not the only one, and the
  two layers are independent on purpose.
- **Never remove a comment explaining why something is the way it is** without checking
  whether it is one of the four types listed in [Conventions](#conventions). Every one of
  them documents a bug that was expensive to find.
- **Never add a build step, a bundler, or a `package.json`** without treating it as an
  architectural decision that changes the project's core property. Write it down here
  first.
- **Never use `git reset --hard` on `main`.** Use `git revert`. The history is a product
  artifact.
- **Never drive Chrome for automated testing.** Use Edge. See
  [Browser Testing](#browser-testing).
- **Never remove the `[skip ci]` marker** from the workflow's commit message.
- **Never edit `data/vix.js` by hand.** It is generated. A manual edit is overwritten on
  the next successful run and, worse, could produce a file that does not parse.

### How to Verify a Change

There is no test suite. Verification is manual and this is the procedure.

**For any change:**

1. Open `index.html`, `strategy.html`, and `custom.html` directly from disk, by
   double-clicking. All three must render.
2. Open DevTools on each. The console must be empty. A single error usually means the
   whole boot script died.
3. Confirm the VIX number, the tier banner, and the status badge all appear on
   `strategy.html` and `custom.html`, and the hero gauge on `index.html`.
4. Resize to 375px wide. Nothing may overflow horizontally.
5. Click Refresh on `strategy.html`. The value should repaint and the timestamp update.
6. If the change touched the Custom page: enter a ticker, save, confirm the legend, table,
   and chart labels all update, then Reset and confirm the defaults return.

**Additionally, for a strategy change:** compare the five rows of the tier reference table
on `strategy.html` against the hardcoded table on `index.html` against the table in
[Strategy Logic](#strategy-logic) in this document. All three must agree.

**Additionally, for a design change:** check both a wide viewport and each of the three
breakpoints (900px, 768px, 480px). Check the hover state of anything you touched.

**Additionally, for a data pipeline change:** trigger `update-vix.yml` manually via
`workflow_dispatch` and confirm the run succeeds and `data/vix.js` changes.

**Browser:** use Microsoft Edge for anything automated or headless. Never Chrome. See
[Browser Testing](#browser-testing).

### After the Change

1. **Update `docs/PATCHNOTES.md`.** Add an entry with a new semantic version, today's
   date in `YYYY-MM-DD`, and the change described in past tense under Added, Changed,
   Fixed, or Removed. This is not optional; the changelog is a product artifact and the
   file itself says not to skip versions.
2. **Update the version header** in `docs/PRD.md` and `docs/DESIGN.md` if either changed.
   They are maintained by hand.
3. **Update the [Milestone Table](#milestone-table)** if a planned item shipped.
4. **Update [Known Technical Debt](#known-technical-debt)** if you created or paid down
   any.
5. **Update [Documentation Versus Reality](#documentation-versus-reality)** if you
   resolved a discrepancy. Do not delete the row; annotate it.
6. **Update [Risks and Open Questions](#risks-and-open-questions)** if you answered one.
   Mark it answered, fold the answer into the relevant section, and leave the question.
7. **Commit** as `type: lowercase imperative description (vX.Y.Z)`.
8. **Push to `main`**, then verify the live site within a few minutes.

### Versioning Rule

Semantic versioning, as the project has actually applied it:

- **MAJOR** for a fundamentally different product surface. Reserved for v2.0.0 onward,
  and used in the roadmap to mean "a significant new capability", not a breaking change,
  since there is no API to break.
- **MINOR** for a new user-visible feature. v1.1.0 (data pipeline), v1.2.0 (custom
  builder).
- **PATCH** for fixes, configuration changes, and documentation. v1.0.11 was a full
  documentation audit released as a patch, which is the precedent v1.2.1 follows.

---

## Press Release

*Written as a launch announcement. The dateline is preserved from the original 2026-06-08
document, because a press release records a moment rather than the current state. The
Custom builder described in the FAQs below shipped later, on 2026-07-09.*

**FOR IMMEDIATE RELEASE**

### VIX Strategy Launches Free Browser Tool That Turns Market Fear Into a Portfolio Action Plan

*A rules-based allocation dashboard shows investors exactly how to position their ETF
portfolio based on the live CBOE Volatility Index. No signup, no backend, no guesswork.*

**ONLINE, June 8, 2026.** Azqato today launched VIX Strategy, a free browser-based tool
that reads the CBOE Volatility Index and instantly computes a rules-based portfolio
allocation across four exchange-traded funds: BIL (Treasury bills), SPY (the S&P 500),
QQQ (the Nasdaq-100), and TQQQ (a 3x leveraged Nasdaq fund). It is available now at
https://azqato.github.io/vix/ and requires no account, no payment, and no software
install. It is built for self-directed long-term investors who want a mechanical rule
instead of a gut call at the exact moment their instincts are least reliable.

**The problem.** When markets crash, most people do the wrong thing. The financial press
fills with reasons to sell, portfolios turn red, and the investor who spent years saying
they would buy the dip discovers that buying the dip feels, in the moment, like catching
a falling knife. There is no shortage of people telling you the VIX has spiked. There is a
severe shortage of anything telling you what to actually do about it, in numbers, before
you have time to talk yourself out of it.

**The solution.** VIX Strategy turns the fear gauge into an instruction. The tool watches
one number, the VIX, and sorts it into one of five bands from complacency to crisis. Each
band has a fixed set of percentages attached to it, decided in advance, when nobody was
panicking. When fear is low, the strategy holds more in Treasury bills and the broad
market. As fear climbs, it shifts step by step toward growth funds, so that the portfolio
is most aggressive exactly when prices are lowest and recoveries have historically been
strongest. The site shows the current number, the band it falls into, the exact
percentages, and a chart of the resulting portfolio. That is the whole product. There is
nothing to configure and nothing to sign up for.

"I kept watching the VIX spike and knowing I should be doing something, but I never had a
clear answer for exactly what," said Dana Ruiz, a software engineer who has been managing
their own retirement account for eleven years. "I would read six articles and end up doing
nothing. Now I open one page and it tells me the number. I do not have to trust my own
judgment on the worst day of the market, which is good, because on that day my judgment is
the last thing I should be trusting."

**What it does not do.** VIX Strategy does not connect to a brokerage, does not place
trades, and does not know anything about the person using it. It stores nothing on any
server. It is an educational tool, and the strategy it describes carries real risk of
severe loss: leveraged funds like TQQQ have lost more than 90 percent of their value in
past downturns, and the site says so on every page. It is not financial advice.

**Get started.** Visit https://azqato.github.io/vix/ and open the Dashboard. There is
nothing to install and nothing to sign up for.

**About Azqato.** Azqato builds small, free, browser-based tools that do one thing
clearly and keep nothing about the people who use them. Its projects are public and
readable, run without accounts or servers, and are maintained in the open. More at
https://azqato.github.io/.

---

## Frequently Asked Questions

### External FAQ

**1. What does this tool do?**
It reads the current CBOE Volatility Index, the VIX, and shows you exactly how to divide a
portfolio across four exchange-traded funds based on a preset rules-based strategy. It
recalculates whenever the VIX moves into a different band.

**2. Who is it for?**
Self-directed investors with a long time horizon, at least ten years, who are comfortable
with leveraged funds and want a mechanical rule rather than a judgment call. It assumes
you already know what an ETF is. It is not designed for someone investing money they will
need soon.

**3. How do I use it?**
Open the Dashboard. Read the VIX number and the tier it falls into. Read the four
percentages. Compare them to what you currently hold. If they differ, rebalance in your
own brokerage account to match. Come back monthly, on the first trading day, and any time
you notice the VIX has crossed into a different band.

**4. Is it free? Is there a paid tier?**
It is free, entirely, with no tiers, no trial, and no upsell. There is no signup, no
payment, and no email required. There are no plans to charge for it. It costs nothing to
run, because it has no server.

**5. Where is it available?**
Anywhere with a web browser and an internet connection. There are no regional
restrictions, because there is nothing to restrict: the site does not know who or where
you are. The data it describes is US market data, so it is most useful to someone
investing in US funds.

**6. What data does it use? Does it collect anything about me?**
It uses one number: the current VIX value, sourced from Yahoo Finance's public market data.
It collects nothing about you. No analytics, no cookies, no tracking, no account. Your
browser stores the last VIX value it saw, and if you use the Custom page, the four tickers
you typed. Both stay in your browser and are never transmitted anywhere.

**7. How current is the VIX value?**
Less current than the word "live" on the badge suggests, and this is worth understanding.
The value is refreshed by an automated job that runs several times per weekday during
market hours, and your browser caches whatever it last saw for up to 30 minutes. The
timestamp shown next to the value is the honest signal: it tells you when the market
actually quoted that number. Check it. The Refresh button clears your browser's cache and
re-reads the latest published value, but it cannot produce a number newer than the last
successful automated update.

**8. What are BIL, SPY, QQQ, and TQQQ?**
BIL holds very short-term US Treasury bills; it is close to cash and very low risk. SPY
tracks the S&P 500, the broad US market. QQQ tracks the Nasdaq-100, which is heavily
weighted toward large technology companies. TQQQ aims to deliver three times the daily
move of the Nasdaq-100, which amplifies both gains and losses.

**9. Can I use my own funds instead of those four?**
Yes. The Custom tab lets you enter your own ticker for each of the four risk categories:
Risk Off, Diversify, Risk On, and Full Risk. The tier boundaries and the percentages stay
exactly the same; only the fund in each slot changes. Your choices are saved in your
browser only. Note that tickers you enter are not currently checked against a live quote,
so verify the symbol yourself before relying on it.

**10. Can I lose money following this strategy?**
Yes, and potentially a great deal. TQQQ lost roughly 80 percent of its value during the
2022 bear market and more than 90 percent during the 2020 crash. This strategy is designed
for money you will not need for at least ten years and can afford to watch fall by 70 to
95 percent without selling.

**11. Why does the strategy buy more of the risky fund when the market is scary?**
Because that is when it is cheapest, and because historically the strongest recovery
periods have followed the highest fear readings. The strategy is a precommitment: you
decide the rule when you are calm, so that you follow it when you are not. It only works
if you actually follow it.

**12. Does it tell me when to buy or sell?**
It tells you what percentage of your portfolio each fund should represent right now. If
your holdings differ from that target, you rebalance to match. You place the trades
yourself, in your own account.

**13. How often should I rebalance?**
Monthly, on the first trading day of the month. Additionally, rebalance immediately
whenever the VIX crosses into a different tier, in either direction.

**14. Does it track my portfolio or my trades?**
No. You enter no personal information and nothing is stored on any server. Your browser
remembers the last VIX number so the page loads instantly next time, and nothing else.

**15. What does it need to run? Will it work on my phone?**
Any browser from roughly 2022 onward. It works on phones down to 375 pixels wide, which
covers every current handset. There is no app to install and no extension required. If you
download the files, they even work opened directly from disk, though the chart needs an
internet connection to draw.

**16. Does it integrate with my brokerage?**
No, and it is not planned. Connecting to a brokerage would require account linking,
per-broker API keys, and regulatory obligations that are far outside what a free tool with
no server can responsibly take on.

**17. How is this different from just looking up the VIX?**
Looking up the VIX gives you a number. This gives you the four percentages that number
implies, computed the same way every time. The difference is the difference between
information and an instruction, and in a panic that difference is most of the value.

**18. How is it different from a robo-advisor?**
A robo-advisor holds your money, charges a fee, asks you questions, and diversifies you
broadly. This holds nothing, charges nothing, asks nothing, and deliberately concentrates
into technology and leverage at exactly the moments most advisors would tell you to be
cautious. It is a narrower and more aggressive tool, aimed at someone who has already
decided they want this specific approach.

**19. What does it not do, in this version?**
It does not show historical VIX data or a chart over time. It does not backtest, so it
cannot show you how the strategy would have performed. It does not verify that a ticker
you type into the Custom page is real. It does not track your holdings or tell you the
dollar amounts to trade. It does not notify you when a tier changes. All of those are on
the roadmap; none of them exist today.

**20. What happens if the data source goes down?**
The page shows you the most recent value it has, marked as cached or stale, rather than
breaking or showing a blank. If it has never successfully loaded a value it shows an
explicit error and a prompt to refresh. It is built so that a data failure is visible and
recoverable rather than silent.

**21. Is this financial advice?**
No. It is an educational and informational tool. Past performance is not indicative of
future results. Consult a qualified financial advisor before making investment decisions,
particularly ones involving leveraged funds.

**22. How do I get help, or report something wrong?**
Through the Support link in the site's navigation, which goes to
https://azqato.github.io/support.html. There is no ticketing system and no support SLA;
this is a free tool maintained by one person.

**23. Is there anything to learn before I can use it?**
No onboarding, no tutorial, and no setup. If you know what an ETF is and how to place a
trade in your brokerage account, you already know everything the tool assumes. The About
page explains the reasoning behind the strategy in full if you want it, and reading it is
strongly recommended before acting on anything.

**24. Can I read or copy the code?**
The source is public and readable on GitHub at https://github.com/Azqato/vix. Note that
the repository does not currently include a license file, so formally the default is all
rights reserved. If you want to reuse it, ask first. Making the licensing explicit is a
known outstanding item.

### Internal FAQ

**1. Why build this as a fully static site with no backend?**
Zero operational cost, zero server maintenance, and zero data-collection liability. GitHub
Pages is free and globally distributed. VIX data is refreshed by a scheduled workflow that
commits a static file and never serves a live request. Adding a real backend would
introduce billing, uptime obligations, and security surface with no user-visible benefit.

**2. What is the ROI rationale?**
Direct cost is zero: no hosting, no data fees, no dependencies to license. The investment
is maintainer time. The return is a public, credible artifact that demonstrates the
strategy, serves as a portfolio piece, and can be pointed at rather than re-explained.
That framing matters, because it means the project does not need traffic to justify
itself, which is why the absence of analytics is tolerable.

**3. What is the single number that tells us it is working?**
Weekly unique visitors to `strategy.html`, on the reasoning that reaching the dashboard
means the pitch worked and repeat visits mean the tool is useful. It is currently
unmeasurable, which is a real problem with the metric rather than with the product. See
open question 5.

**4. What happens if the committed data file and the CORS proxy both fail?**
The app falls back to the last cached value with a STALE badge. If no cache exists it
shows an ERROR badge and explicit error text. The page never crashes and the layout never
breaks. This is tenet 3 in practice.

**5. Why TQQQ? Is 3x leverage not extremely risky?**
Yes, deliberately. The strategy targets aggressive long-term investors who understand
volatility decay. TQQQ is capped at 25 percent at extreme fear and starts at 5 percent at
low fear. The risk disclosure is explicit about 70 to 95 percent drawdown potential and the
tool assumes a ten-year-plus horizon.

**6. How does the 30-minute cache avoid showing misleadingly stale data?**
The VIX moves slowly enough that a 30-minute-old reading is actionable for a monthly
rebalancing decision, and the tradeoff buys instant page loads. The honest caveat is that
the cache is not the binding constraint on freshness: the scheduled refresh is. See D-01.

**7. What must be true for this strategy to outperform buy-and-hold?**
Three things: the VIX remains mean-reverting over the investor's horizon, QQQ and TQQQ
continue their long-term upward trend, and the investor holds through 70 to 95 percent
drawdowns without selling. None is guaranteed. The strategy is a framework, not a promise.

**8. Is this financial advice, and what is our exposure?**
No, and the disclaimer appears on every page. It is educational, aimed at people who have
already decided to explore VIX-based allocation. The current gap is that the disclaimer is
below the fold on every page and set in a color that fails contrast standards, which is
open question 3 and open question 4 and should be treated as a priority rather than a
polish item.

**9. Can this be forked and customized?**
The code is public and intentionally simple: four JavaScript files, one stylesheet, three
HTML pages, no build step. Anyone can clone it and replace the allocation table. The
licensing is not yet explicit, which undercuts the invitation. See open question 2.

**10. How is the VIX data sourced, and how fragile is that?**
Yahoo Finance's internal JSON API at `query1.finance.yahoo.com/v8/finance/chart/^VIX`. A
scheduled workflow fetches it directly and commits the value; the browser falls back to
the same endpoint through a public CORS relay if the committed file is unavailable. No API
key either way. It is fragile in a specific way: the endpoint is undocumented and
unsupported, so a change to its response shape breaks both paths at once, and there is no
alert that would tell us.

**11. Why not use a paid data API with an SLA?**
Most financial data APIs require keys, accounts, and billing, all of which conflict with
the no-backend and no-data-collection constraints. A key cannot be kept secret in a static
site. The free unauthenticated endpoint, fetched server-side on a schedule, is the best
fit available.

**12. Does the Custom builder verify that entered tickers are real?**
No. Input is free-text, sanitized to a plausible shape but not checked against a quote,
and the UI says so explicitly. Live verification is planned for v2.3.0 using the same
fetch pattern already proven for the VIX itself.

**13. Does the Custom builder undermine the "the rule is the product" tenet?**
It was scoped so that it does not. Users can change which instrument fills each risk slot;
they cannot change the tier boundaries or the percentages. Substitution is portability.
Tuning would have been rejected.

**14. What is the roadmap direction, and why that order?**
Data visualization first (sparkline, then percentile rank), because both make the existing
single number more interpretable without adding a second strategy or a second thing to
maintain. Strategy variants and ticker verification follow. Backtesting and a portfolio
tracker are the two large items and both are far out, the tracker because it needs
persistent user state, which strains the no-backend tenet hardest.

**15. What is the biggest risk to the product right now?**
The data pipeline going quiet without anyone noticing. There is no alerting, no error
reporting, and no uptime monitoring, so a failure is discovered when a human happens to
open the page. As of this audit the published value is roughly seven weeks old and it is
not yet established why. See open question 1.

**16. What would v4.0 look like, and why is it last?**
A portfolio tracker: the user enters their holdings and the app computes the dollar delta
to reach the target allocation, for example "sell $2,400 of BIL, buy $2,400 of TQQQ". It
is the most requested conceivable feature and the most architecturally difficult, because
it needs persistent per-user state, which means either a backend or a
localStorage-only design with a demanding UX. It is last because everything else is
cheaper and the tenets make the easy version of it impossible.
