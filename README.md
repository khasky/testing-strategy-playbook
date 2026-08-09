# Testing Strategy Playbook

[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE) [![Emojery](https://api.emojery.app/badge/github/khasky/testing-strategy-playbook.svg)](https://emojery.app/react?t=github/khasky/testing-strategy-playbook)

Practical testing strategy guide for unit, integration, end-to-end, contract, and reliability-focused test design.

> *If I were setting a testing standard for a product team today, I would optimize for four things first: fast feedback, clear test boundaries, production-shaped integration coverage, and ruthless removal of flaky tests.*

---

## Table of Contents

- [Testing Strategy Playbook](#testing-strategy-playbook)
  - [Table of Contents](#table-of-contents)
  - [Why this exists](#why-this-exists)
  - [Companion playbooks](#companion-playbooks)
  - [The defaults I'd reach for first](#the-defaults-id-reach-for-first)
  - [The tools I'd reach for in a JS/TS repo](#the-tools-id-reach-for-in-a-jsts-repo)
  - [The testing model I would defend](#the-testing-model-i-would-defend)
  - [Unit tests](#unit-tests)
    - [What unit tests are good for](#what-unit-tests-are-good-for)
    - [What I expect from a good unit test](#what-i-expect-from-a-good-unit-test)
    - [What I would avoid](#what-i-would-avoid)
  - [Integration tests](#integration-tests)
    - [What should be integration-tested](#what-should-be-integration-tested)
    - [My preferred approach](#my-preferred-approach)
  - [Contract tests](#contract-tests)
    - [Good uses for contract testing](#good-uses-for-contract-testing)
    - [What matters most](#what-matters-most)
  - [End-to-end tests](#end-to-end-tests)
    - [What I would use them for](#what-i-would-use-them-for)
    - [What I would not do](#what-i-would-not-do)
  - [Visual and accessibility regression](#visual-and-accessibility-regression)
  - [Test data and environments](#test-data-and-environments)
    - [Defaults I prefer](#defaults-i-prefer)
    - [Environment policy](#environment-policy)
  - [CI strategy](#ci-strategy)
    - [I would usually split CI into lanes](#i-would-usually-split-ci-into-lanes)
      - [Fast lane on pull request](#fast-lane-on-pull-request)
      - [Confidence lane on merge](#confidence-lane-on-merge)
      - [Deep lane nightly or scheduled](#deep-lane-nightly-or-scheduled)
  - [Flakiness policy](#flakiness-policy)
    - [My policy would be simple](#my-policy-would-be-simple)
  - [Coverage, but not cargo cult coverage](#coverage-but-not-cargo-cult-coverage)
    - [What I would use it for](#what-i-would-use-it-for)
    - [What I would not use it for](#what-i-would-not-use-it-for)
  - [Mutation testing](#mutation-testing)
  - [AI-generated tests](#ai-generated-tests)
  - [A release-minded test matrix](#a-release-minded-test-matrix)
  - [Things I would avoid](#things-i-would-avoid)
  - [License](#license)

---

## Why this exists

A lot of teams say they care about quality, but what they really have is a pile of tests with no theory behind them:

- unit tests that mock everything until they verify nothing;
- end-to-end tests trying to compensate for weak integration coverage;
- flaky suites everyone mistrusts;
- slow pipelines that train people to bypass signal;
- coverage metrics treated like product truth.

This README is a better default.

It turns testing from "more tests" into a strategy.

---

## Companion playbooks

These repositories form one playbook suite:

- [AI-Assisted Engineering Playbook](https://github.com/khasky/ai-assisted-engineering-playbook) — agent workflows, guardrails, and quality control for AI-heavy teams
- [API Design Playbook](https://github.com/khasky/api-design-playbook) — versioning, pagination, idempotency, error contracts, and webhooks
- [Auth & Identity Playbook](https://github.com/khasky/auth-identity-playbook) — sessions, tokens, OAuth, and identity boundaries across the stack
- [Backend Architecture Playbook](https://github.com/khasky/backend-architecture-playbook) — APIs, boundaries, OpenAPI, persistence, and errors
- [Best of JavaScript](https://github.com/khasky/best-of-javascript) — curated JS/TS tooling and stack defaults
- [Caching Playbook](https://github.com/khasky/caching-playbook) — HTTP, CDN, and application caches; consistency and invalidation
- [Code Review Playbook](https://github.com/khasky/code-review-playbook) — PR quality, ownership, and review culture
- [CTO Playbook](https://github.com/khasky/cto-playbook) — org design, hiring, technical strategy, budgets, and due diligence
- [DevOps Delivery Playbook](https://github.com/khasky/devops-delivery-playbook) — CI/CD, environments, rollout safety, and observability
- [Engineering Lead Playbook](https://github.com/khasky/engineering-lead-playbook) — standards, RFCs, and technical leadership habits
- [Frontend Architecture Playbook](https://github.com/khasky/frontend-architecture-playbook) — React structure, performance, and consuming API contracts
- [Git Collaboration Playbook](https://github.com/khasky/git-collaboration-playbook) — branching, stacked PRs, merge queues, and CI collaboration at scale
- [Marketing and SEO Playbook](https://github.com/khasky/marketing-and-seo-playbook) — growth, SEO, experimentation, and marketing surfaces
- [Messaging & Async Playbook](https://github.com/khasky/messaging-and-async-playbook) — queues, events, outbox, idempotent consumers, and retries
- [Monorepo Architecture Playbook](https://github.com/khasky/monorepo-architecture-playbook) — workspaces, package boundaries, and shared code at scale
- [Node.js Runtime & Performance Playbook](https://github.com/khasky/nodejs-runtime-performance-playbook) — event loop, streams, memory, and production Node performance
- [Observability Playbook](https://github.com/khasky/observability-playbook) — logs, traces, metrics, SLOs, and production visibility
- [React Cross-Platform Playbook](https://github.com/khasky/react-cross-platform-playbook) — shared React UI and logic across web and native with TypeScript
- [Software Design Playbook](https://github.com/khasky/software-design-playbook) — separation of concerns, composition, and module boundaries
- [State Management Playbook](https://github.com/khasky/state-management-playbook) — client state architecture, MobX, and choosing a state layer
- [Styling Architecture Playbook](https://github.com/khasky/styling-architecture-playbook) — type-safe styling, design tokens, and theming at scale
- [Sysadmin Operations Playbook](https://github.com/khasky/sysadmin-operations-playbook) — servers, backups, DNS, TLS, identity, and the self-hosted ops stack
- **Testing Strategy Playbook** — unit, integration, contract, E2E, and CI-friendly test layers

---

## The defaults I'd reach for first

If I were designing a test strategy today, I would usually default to this:

- **Unit tests** for pure logic, transformations, and policy decisions
- **Integration tests** for boundaries that matter: database, queue, cache, API, filesystem
- **Contract tests** where one service depends on another service or public API
- **End-to-end tests** for a small number of business-critical paths
- **CI lanes** split into fast PR checks and slower merge/nightly checks
- **Test data** that is explicit, minimal, and reusable
- **Flaky tests** treated as defects, not personality traits
- **Coverage** used as a weak signal, never the whole story

That mix gives teams speed *and* realism.

---

## The tools I'd reach for in a JS/TS repo

The strategy chapters in this playbook are deliberately tool-agnostic, and they stay that way. This section is the one exception: if I were setting up a JS/TS repo today, this is the current default kit.

- **Vitest** as the runner — fast, ESM-native, and low-friction in modern toolchains; Jest is fine where it is already the incumbent
- **Testing Library** for components — it asserts what a user sees, not what internal state looks like
- **MSW** for HTTP mocking — it intercepts at the network boundary, so the real client code still runs
- **Testcontainers** for integration lanes — real databases and queues in disposable containers instead of in-memory imitations
- **Playwright** for E2E — reliable auto-waiting, tracing, and parallelism out of the box
- **Pact** (or OpenAPI-based verification) for contract tests — consumer expectations verified against the provider before release, not after

The strategy survives tool churn.  
The kit rotates, and that is fine.

---

## The testing model I would defend

My mental model is simple:

1. **Test logic close to where it lives**
2. **Test integrations where systems actually meet**
3. **Test user journeys sparingly but seriously**
4. **Test contracts before breakage reaches production**
5. **Optimize for trust in the signal**

The goal is not to maximize test count.  
The goal is to reduce uncertainty where it matters most.

### Why the shape arguments never end

The pyramid comes from Mike Cohn's *Succeeding with Agile* (2009), written when a browser test meant Selenium driving a real browser: slow, flaky, expensive, so put as few tests there as possible. Kent C. Dodds' "testing trophy" (2018) reweights toward integration for frontend work, where the units are small and most real bugs live in how components, routing, and data fetching meet.

Both are answers to the same question — *where is a test most expensive, and where do bugs actually appear?* — asked about different stacks a decade apart. Neither is a law. When someone cites a shape, ask what their tests cost and where their bugs come from; that is the argument that actually decides the mix.

Google's escape from this is worth stealing: it classifies tests by **size** — what a test is permitted to touch (small: single process, no network or filesystem; medium: local resources; large: multiple machines) — rather than by the words unit and integration. A size is enforceable and unambiguous. "Is this a unit test?" never is.

---

## Unit tests

### What unit tests are good for

Unit tests shine when they cover:

- domain rules;
- data transformations;
- validation;
- calculation logic;
- state transitions;
- error branching that is expensive to reason about mentally.

### What I expect from a good unit test

- one clear behavior under test;
- minimal setup;
- readable inputs and expected outputs;
- no accidental integration concerns;
- little or no mocking unless collaboration behavior is the thing being tested.

### What I would avoid

- mocking basic language/runtime behavior;
- snapshotting giant objects without understanding them;
- asserting implementation trivia instead of business behavior.

---

## Integration tests

Integration tests are where mature teams earn their confidence.

### What should be integration-tested

- repository behavior against a real database;
- API handlers against the actual web stack;
- queue consumers against realistic message payloads;
- cache and persistence interactions;
- external client adapters with local fakes or sandbox systems.

### My preferred approach

Use real infrastructure in containers whenever practical.

A slower, realistic integration test is often worth ten brittle, over-mocked "unit" tests that never touched the real boundary.

---

## Contract tests

Where services or teams integrate, contract drift becomes expensive.

### Good uses for contract testing

- consumer-provider API boundaries;
- frontend-backend schema guarantees;
- event payload compatibility;
- webhook delivery contracts.

### What matters most

- the contract is versioned;
- the provider validates compatibility before release;
- the consumer tests against the expected contract, not just today's lucky implementation.

Contract tests are often the missing middle between integration tests and production incidents.

---

## End-to-end tests

End-to-end tests are valuable, but only in the right dosage.

### What I would use them for

A short, high-value set of flows such as:

- sign in / sign up
- checkout / payment
- core CRUD flow
- permissions boundary
- deployment smoke check
- one or two critical admin paths

### What I would not do

- cover every permutation end-to-end;
- encode unstable UI details into every assertion;
- let E2E become the main source of confidence.

When a team has hundreds of brittle E2E tests, it usually means other test layers are underdeveloped.

---

## Visual and accessibility regression

Some regressions never touch a function's return value.

- screenshot diffs for design-system components — Playwright snapshots, or a Chromatic-style review flow where a human approves intended changes;
- axe-core checks wired into component tests for the basics: labels, roles, contrast, focus order;
- keep visual suites small and intentional — a folder of stale snapshots everyone rubber-stamps is worse than no visual tests at all.

Snapshot noise kills trust.  
Every visual test should protect something someone would notice breaking.

---

## Test data and environments

Bad test data quietly ruins good suites.

### Defaults I prefer

- factories/builders for readable object setup;
- small fixtures with clear intent;
- seed data owned by tests, not magical shared state;
- disposable test environments where possible;
- representative but sanitized data.

### Environment policy

A healthy setup often looks like:

- **local:** unit + focused integration
- **PR CI:** unit + focused integration + smoke
- **merge/main:** broader integration + contract + core E2E
- **nightly:** slow suites, migration checks, resilience scenarios

---

## CI strategy

One pipeline is rarely enough.

### I would usually split CI into lanes

#### Fast lane on pull request
- linting
- type checks
- unit tests
- fastest integration tests
- security basics where cheap

#### Confidence lane on merge
- broader integration suite
- contract verification
- migration checks
- selected E2E tests

#### Deep lane nightly or scheduled
- full E2E matrix
- performance baselines
- resilience / chaos scenarios if the team is ready
- cross-version compatibility checks

The right question is not "did everything run?"  
It is "did the right signal arrive at the right stage?"

---

## Flakiness policy

Flaky tests are a product problem disguised as a test problem.

### My policy would be simple

- a flaky test is a defect;
- repeated flakes trigger quarantine or removal;
- quarantined tests stay visible, owned, and time-bound;
- teams should track flake rate and time-to-fix.

Nothing corrodes engineering culture faster than a red pipeline everyone ignores.

### The scale of it, measured

Google's published figures (John Micco, ~2016–2017) put roughly **1.5% of test runs** as flaky, while **about 16% of their 4.2 million tests** had shown flakiness at some point.

Those two numbers are the whole policy argument. A 1.5% per-run rate looks like background noise on a daily dashboard, which is why flakiness gets tolerated; the 16% is the same phenomenon accumulated over time, and it is what actually blocks releases. Engineers of that calibre did not write worse tests than yours — flakiness is a property of systems with concurrency, clocks, and networks, not a sign of carelessness.

Which is why the response has to be structural: quarantine, ownership, a tracked rate. "Try harder" has already been tried at a scale you will not reach.

---

## Coverage, but not cargo cult coverage

Coverage is useful when interpreted carefully.

### What I would use it for

- spotting obviously untested areas;
- preventing accidental drops in critical modules;
- guiding review conversations.

### What I would not use it for

- proving quality;
- rewarding teams for test quantity;
- forcing arbitrary thresholds on every package regardless of risk.

A test suite with lower coverage and stronger assertions is often healthier than a high-coverage suite full of shallow checks.

---

## Mutation testing

Coverage says the code ran.  
Mutation score says the tests noticed.

A mutation tool flips conditions, deletes statements, and swaps operators in your code. If the suite stays green, the mutant survives — and a surviving mutant is a behavior your tests silently do not defend.

- **Stryker** is the JS/TS option I would reach for;
- run it on critical modules or changed files, not the whole repo on every build — it is too expensive for that;
- treat surviving mutants as review findings: add the missing assertion, or accept the risk explicitly.

Mutation testing is the honest audit of a suite that coverage numbers flatter.

The technique was described in 1978 (DeMillo, Lipton and Sayward) and sat unused for decades for one reason: mutating a program and re-running the suite once per mutant was unaffordable. Nothing conceptual changed — CPU got cheap. Worth remembering when a tool feels novel; it is often an old idea whose cost finally dropped, which also tells you where its cost still bites.

---

## AI-generated tests

AI is useful as a test-writing assistant. It is dangerous as the sole author.

- good for scaffolding suites and brainstorming edge cases a tired human skips;
- tests generated from the implementation inherit its blind spots — they assert current behavior, bugs included;
- generate from the contract or spec, not from the code under test;
- review generated asserts with the same rigor as production code;
- a green AI test proves nothing until it has failed once — make it fail on purpose before trusting it.

Workflow and guardrails for AI-heavy teams are covered in the [AI-Assisted Engineering Playbook](https://github.com/khasky/ai-assisted-engineering-playbook).

---

## A release-minded test matrix

If the team ships web or backend products, I would usually want this shape:

- **Pre-commit or local**
  - unit tests
  - lint
  - type checks

- **Pull request**
  - unit
  - selected integration
  - contract linting
  - smoke E2E if cheap

- **Pre-merge or main**
  - integration suite
  - migration checks
  - contract verification
  - critical-path E2E

- **Pre-release**
  - rollback rehearsal
  - production-like smoke
  - alert sanity
  - canary validation if applicable

Testing is not one wall. It is a sequence of increasingly expensive confidence gates.

---

## Things I would avoid

- treating end-to-end tests as the default answer to every quality gap;
- measuring engineering quality with one coverage percentage;
- shared mutable test fixtures across suites;
- giant mocks that replicate the production system badly;
- silently tolerated flakes;
- integration tests that require a human to "just run this service first".

---

## License

MIT is a sensible default for a playbook repository like this, but choose the license that fits your sharing goals.
