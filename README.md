# VIX Strategy

VIX Strategy is a free website that reads the CBOE Volatility Index (the VIX, widely
called the market's "fear gauge") and turns it into a specific portfolio allocation:
what percentage of your money a rules-based strategy says should sit in each of four
exchange-traded funds right now.

**Live site:** [https://azqato.github.io/vix/](https://azqato.github.io/vix/)

## What the site offers

**About** is the case for the strategy. It explains why static buy-and-hold portfolios
leave returns behind during market crashes, why the VIX is a useful signal (it tends to
spike during panics and then fall back toward its long-run average), and how the
strategy responds by shifting money toward growth funds when fear is high and back
toward safety when markets are calm. It carries a live VIX reading at the top and a
full risk disclosure at the bottom.

**Dashboard** is the working tool. It shows the current VIX value, which of five fear
tiers that value falls into, and the exact percentage split across the four funds for
that tier: BIL (short-term Treasury bills), SPY (the S&P 500), QQQ (the Nasdaq-100),
and TQQQ (a 3x leveraged Nasdaq fund). A donut chart, a breakdown table, and a
reference table covering all five tiers make the current position and the full rule set
readable at a glance.

**Custom** lets you swap the four default funds for tickers you would rather hold. The
tier boundaries and the percentages stay exactly as they are; only the fund in each of
the four risk slots changes. Your choices are saved in your own browser and never sent
anywhere.

There is no account, no signup, no payment, and no tracking. Nothing you type is
transmitted off your machine.

## Who it is for

Self-directed long-term investors who are comfortable with leveraged funds and want a
mechanical rule instead of a gut call, researchers looking at systematic allocation
strategies, and developers who want to read or fork a deliberately simple, build-free
web app.

This is an educational tool, not financial advice. The strategy it describes can lose a
large amount of money, and the site says so on every page.

## Current status

Live and in active development. The core strategy dashboard and the custom builder have
both shipped. Further data-visualization features are planned.

## Where to learn more

Setup, architecture, design system, security model, roadmap, metrics, and the full
change history are in [`/docs`](./docs):

| File | What is in it |
|------|---------------|
| [docs/PRD.md](docs/PRD.md) | Everything: product scope, users, tenets, roadmap, metrics, runbook, architecture, conventions, security, FAQ |
| [docs/DESIGN.md](docs/DESIGN.md) | Colors, typography, spacing, breakpoints, component rules, accessibility, motion |
| [docs/PATCHNOTES.md](docs/PATCHNOTES.md) | Every release, dated, with what changed and why |
