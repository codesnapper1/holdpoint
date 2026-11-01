<div align="center">

# HOLDPOINT

### Consequential Action Control Layer

**Intent is not authority. Give consequential actions a boundary, a decision, and an accountable trail.**

<p>
  <img src="docs/walkthrough/landing-page.jpg" alt="Holdpoint landing page featuring Authority before impact, the action control story, and navigation to the policy graph, coverage, evidence, and console" width="100%">
</p>

<sub>Interactive local simulation · seeded data · no live destination actions</sub>

</div>

---

## Contents

- [What Holdpoint is](#what-holdpoint-is)
- [Walkthrough](#walkthrough)
- [Feature guide](#feature-guide)
- [How the system is structured](#how-the-system-is-structured)
- [Run it locally](#run-it-locally)
- [Checks](#checks)
- [API route index](#api-route-index)
- [Repository map](#repository-map)
- [Prototype boundaries](#prototype-boundaries)

## What Holdpoint is

Holdpoint is a working prototype of a control layer between an AI agent and consequential tools. It explores a simple rule: **an agent may propose an action, but the action must be described, checked, and authorized before the system simulates an effect.**

A chat message is treated as evidence of intent—not as permission. Holdpoint turns that intent into a typed action contract with a named actor, destination, resource, parameters, impact, reversibility, and idempotency key. Deterministic checks then decide whether the proposal can proceed, needs a bounded approval, must be held, or should be denied.

The project includes a React control room, an Express API, a deterministic policy and execution engine, seeded destinations, a local evidence ledger, recovery drills, and scenario runners.

## Walkthrough

The screenshots below are still frames from the project’s narrated demo. Names, amounts, assets, and outcomes are seeded demonstration data.

### 1. Start at the control room

The Overview brings the current persona’s protected surface, approval queue, route coverage, recovery copies, and evidence activity into one view. Switch between consumer and enterprise modes to see different work contexts.

<p align="center">
  <img src="control-room-preview.png" alt="Overview dashboard for the selected Holdpoint persona" width="100%">
</p>

### 2. Turn a request into a reviewable contract

In Agent workspace, choose a persona and submit a prompt. The example below requests deletion of original photos. Holdpoint drafts a structured contract, identifies the destination and impact, runs pre-queue validation, and quarantines the harmful proposal.

<p align="center">
  <img src="docs/walkthrough/agent-contract.jpg" alt="Agent workspace showing a quarantined delete-originals draft and its typed action contract" width="100%">
</p>

**What to notice:** the raw prompt does not become authority. The contract carries the resolved target, impact, reversibility, canonical parameters, and validation result.

### 3. Review a bounded approval

The Approval inbox presents the exact action, proposer, destination, resource, risk, policy, and expiry window. The reviewer can authorize or reject the specific proposal; changing a bound target invalidates the earlier approval.

<p align="center">
  <img src="docs/walkthrough/approval-inbox.jpg" alt="Approval inbox showing a sample treasury wire with its target, policy, risk, and expiring authorization" width="100%">
</p>

### 4. Define policy and inspect route coverage

Policy studio drafts plain-language requirements into typed rules and checks them against existing rules. Route coverage shows which paths are mediated and which remain gaps. An observed or unconnected route is not presented as protected.

<p align="center">
  <img src="docs/walkthrough/policy-studio.jpg" alt="Policy studio compiling a plain-language requirement into a typed rule" width="100%">
</p>

<p align="center">
  <img src="docs/walkthrough/route-coverage.jpg" alt="Route coverage inventory showing mediated routes and unconnected gaps" width="100%">
</p>

### 5. Exercise recovery and inspect the evidence

Recovery lets an operator select an independent snapshot and run a restore drill. The Evidence ledger links recorded stages and includes controls to verify the chain or simulate tampering.

<p align="center">
  <img src="docs/walkthrough/recovery-workspace.jpg" alt="Recovery workspace with an independent snapshot and restore-drill control" width="100%">
</p>

<p align="center">
  <img src="docs/walkthrough/evidence-ledger.jpg" alt="Evidence ledger with chained records and integrity controls" width="100%">
</p>

### 6. Run paired acceptance scenarios

The Acceptance lab evaluates harmful attempts beside their legitimate controls against the local engine and seeded destination state. The repository includes 28 scenarios; the screen below shows the demonstration interface, not a production certification.

<p align="center">
  <img src="docs/walkthrough/acceptance-lab.jpg" alt="Acceptance lab showing paired harmful and legitimate scenarios" width="100%">
</p>

## Feature guide

| Area | What it does |
| --- | --- |
| **Personas and modes** | Consumer and enterprise workspaces with role-specific context, budgets, protected assets, and suggested tasks. |
| **Agent workspace** | Persona chat, draft generation, pipeline view, attack prompts, and stress-test controls. Gemini can help interpret prompts; a local semantic compiler is available without a key. |
| **Typed action contracts** | Capture actor and role, destination, resource and version, canonical parameters, impact, reversibility, delegation, and idempotency. |
| **Inspection and detection** | Track inspected parts, skipped bytes, coverage gaps, and findings such as PII, secrets, nested evasion, suspicious URLs, or OCR gaps. |
| **Policy studio** | Compile owner requirements into typed rules; check authority, destination, action, financial bounds, reversibility, inspection, egress, recovery, and self-approval constraints. |
| **Approval inbox** | Bind approval to a particular action, resource version, destination, policy version, canonical hash, role, and expiry. Support authorize, reject, expire, and target-change invalidation flows. |
| **Route coverage** | Inventory mediated and unconnected paths so gaps remain visible. Traffic observations are not proof that an action was prevented. |
| **Destination inspector** | Inspect seeded destination resources, versions, snapshots, permissions, and simulated outcomes. |
| **Action timeline** | Follow an action through proposal, inspection, decision, approval, dispatch simulation, reconciliation, or recovery. |
| **Evidence ledger** | Review linked event records, verify ledger integrity, and demonstrate how a tampered entry is detected. |
| **Recovery workspace** | Select independent snapshots and exercise restore drills against the local destination model. |
| **Emergency halt** | Drain queued work and reconcile in-flight actions in the simulation. |
| **Simulation and display controls** | Switch appearance, inject failure conditions, reset the in-memory run, and trigger emergency halt. |
| **Acceptance lab** | Run harmful cases alongside legitimate controls against the deterministic engine and destination state. |
| **System dossier and multi-user model** | Browse the system model, actors, roles, destinations, policies, and action vocabulary used by the prototype. |

### Decision and lifecycle vocabulary

Policy decisions are distinct from action lifecycle states.

| Policy decision | Meaning in the prototype |
| --- | --- |
| <code>allow</code> | The rule set permits this action in the current context. |
| <code>stage</code> | Prepare the action but require another control, such as independent approval. |
| <code>sanitize</code> | Apply a bounded transformation before the action continues. |
| <code>escalate</code> | Route the proposal to an authorized reviewer. |
| <code>deny</code> | Reject the action under policy. |
| <code>hold</code> | Stop progress when required information or inspection is unavailable. |

An action then moves through lifecycle states such as <code>proposed</code>, <code>inspecting</code>, <code>decided</code>, <code>staged</code>, <code>approved</code>, <code>dispatched</code>, <code>outcome_unknown</code>, <code>reconciled</code>, <code>completed</code>, <code>denied</code>, <code>held</code>, <code>cancelled</code>, or <code>reverted</code>. If a destination result is ambiguous, the demo models reconciliation before retry.

## How the system is structured

<p align="center">
  <img src="docs/holdpoint-architecture.svg" alt="Holdpoint architecture from user intent through contract validation and policy to simulated destination and evidence ledger" width="100%">
</p>

The main path is:

1. **Capture intent.** A persona submits a natural-language prompt. Gemini is optional and only drafts an interpretation.
2. **Build a contract.** The chat/compiler path produces a typed proposal with canonical parameters and a target.
3. **Validate and inspect.** Schema checks, authorization bounds, detectors, and inspection coverage contribute evidence to the decision.
4. **Apply policy.** The deterministic policy engine evaluates scope, impact, budget, reversibility, inspection, and recovery constraints.
5. **Require authority when needed.** Bounded approvals are tied to the exact proposal and expire; changed parameters can invalidate them.
6. **Simulate execution.** The execution engine works against an in-memory destination registry, records outcomes, and handles ambiguous results.
7. **Record and verify.** Evidence is appended to the local hash-linked ledger; operators can inspect and verify the chain.

### Main modules

- <code>src/engine/chat.ts</code> — Gemini integration and local semantic intent compiler.
- <code>src/engine/types.ts</code> — action, approval, policy, inspection, and destination contracts.
- <code>src/engine/detectors.ts</code> and <code>src/engine/securityEngine.ts</code> — prompt/content analysis and control checks.
- <code>src/engine/policyEngine.ts</code> — deterministic policy evaluation.
- <code>src/engine/executionEngine.ts</code> and <code>src/engine/executionLoop.ts</code> — action lifecycle, approvals, dispatch simulation, reconciliation, and evidence.
- <code>src/engine/destinations.ts</code> — in-memory resources, versions, and recovery snapshots.
- <code>src/engine/scenarios.ts</code> — paired harmful and legitimate scenarios.
- <code>server.ts</code> — Express API and Vite development server.

## Run it locally

### Requirements

- Node.js 22.12 or later recommended. [Vite 8 supports Node.js 20.19+ or 22.12+.](https://vite.dev/guide/)
- npm

### Install and start

~~~bash
git clone https://github.com/codesnapper1/holdpoint.git
cd holdpoint
npm ci
~~~

The application runs without a Gemini key using the local semantic compiler. To enable Gemini-backed chat, copy the environment template and set <code>GEMINI_API_KEY</code>:

~~~bash
# macOS / Linux
cp .env.example .env
~~~

~~~powershell
# Windows PowerShell
Copy-Item .env.example .env
~~~

Then start the server:

~~~bash
npm run dev
~~~

Open [http://localhost:3000](http://localhost:3000). Express serves the API and Vite-powered app from the same local server.

Keep <code>.env</code> private; it is excluded from Git. If you configure Gemini, chat prompts are sent to Google’s Gemini API using the server-side key.

### Build

~~~bash
npm run build
~~~

The build writes frontend assets to <code>dist/</code>. For production-mode static serving through Express, set <code>NODE_ENV=production</code> before running <code>npm start</code>.

## Checks

Run the checks individually:

~~~bash
npm run lint
npm run test:acceptance
npm run test:stress
npm run test:adversarial
~~~

Or run the complete configured suite:

~~~bash
npm test
~~~

These are prototype checks over local engine and destination state. They are not a substitute for security review or production integration testing.

## API route index

The Express API is unauthenticated and intended for local demo use.

| Method | Route | Purpose |
| --- | --- | --- |
| <code>GET</code> | <code>/api/health</code> | Process health and uptime. |
| <code>POST</code> | <code>/api/chat</code> | Persona chat and draft action response. |
| <code>POST</code> | <code>/api/drafts/generate</code> | Generate a draft contract. |
| <code>POST</code> | <code>/api/drafts/validate</code> | Validate a draft against schema and persona bounds. |
| <code>POST</code> | <code>/api/drafts/promote</code> | Promote an accepted draft into the action pipeline. |
| <code>GET</code> | <code>/api/actions</code> | Read action and pending-approval state. |
| <code>POST</code> | <code>/api/actions/submit</code> | Submit a typed action contract. |
| <code>POST</code> | <code>/api/approvals/:id/authorize</code> | Authorize a pending bounded approval. |
| <code>POST</code> | <code>/api/approvals/:id/reject</code> | Reject a pending approval. |
| <code>POST</code> | <code>/api/approvals/:id/expire</code> | Expire an approval. |
| <code>POST</code> | <code>/api/approvals/:id/adjust-ttl</code> | Adjust remaining approval time for the demo. |
| <code>POST</code> | <code>/api/kill-switch</code> | Trigger the emergency halt flow. |
| <code>POST</code> | <code>/api/restore-drill</code> | Run a restore drill against a selected snapshot. |
| <code>POST</code> | <code>/api/scenarios/run</code> | Run one scenario or the complete acceptance set. |
| <code>GET</code> | <code>/api/test/stress-suite</code> | Run the stress suite and return a report. |
| <code>GET</code> | <code>/api/ledger</code> | Read evidence records and route state. |
| <code>POST</code> | <code>/api/ledger/tamper</code> | Simulate an evidence-ledger tamper. |
| <code>GET</code> | <code>/api/ledger/verify</code> | Verify evidence-ledger integrity. |
| <code>POST</code> | <code>/api/reset</code> | Reset in-memory demo state. |

## Repository map

~~~text
src/
  components/       React views for workspace, controls, recovery, and evidence
  engine/           Contracts, policies, detectors, execution, and scenarios
public/
  avatars/          Persona artwork used by the app
  holdpoint-operator-hero.png
docs/
  holdpoint-architecture.svg
  walkthrough/      README screenshots from the narrated demo
server.ts           Express API and Vite development server
test_*.ts           Acceptance, adversarial, and stress test runners
~~~

## Prototype boundaries

- **Local state:** actions, destinations, approvals, and ledger entries are held in memory and reset when the server restarts.
- **No live destination effects:** bank, photo, cloud, repository, email, and identity destinations are simulated; the app does not connect to those providers.
- **Gemini is optional:** when configured, chat prompts leave the machine and are sent to Google’s Gemini API. Without a key, the local semantic compiler is used.
- **No production enforcement:** the demo’s “enforced” route labels, approvals, restore results, and test passes describe the local simulation only.
- **No authentication layer:** the local Express API is open for demo interactions and must not be exposed as a production service.
- **No license file:** this repository does not currently grant reuse rights.

Holdpoint is a product and architecture prototype, not a security product or production control.
