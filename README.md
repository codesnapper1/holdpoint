<div align="center">
  <h1>HOLDPOINT</h1>
  <p><strong>Consequential Action Control Layer</strong></p>
  <p>Let agents propose. Make every consequential action prove its scope before execution.</p>
</div>

<p align="center">
  <img src="control-room-preview.png" alt="Holdpoint control room showing policy coverage, evidence, and an approval queue" width="100%">
</p>

> **A local simulation.** Holdpoint demonstrates how an action can be inspected, checked against policy, approved or stopped, and recorded. Destination effects are simulated in memory; this prototype does not connect to real accounts or dispatch real-world actions.

## The control path

~~~text
Agent intent
    → Typed action contract
    → Content and scope inspection
    → Deterministic policy decision
    → Bounded approval or hold
    → Simulated destination
    → Evidence ledger and recovery
~~~

Holdpoint is built around a simple boundary: **conversation can suggest an action, but policy decides whether that action may proceed.** The control path makes the actor, target, destination, impact, authority, and reversibility visible before a simulated effect is recorded.

## Explore the workspace

| Area | What you can explore |
| --- | --- |
| **Agent workspace** | Consumer and enterprise personas, chat, proposed actions, and typed action details |
| **Policy studio** | Deterministic rules for protected assets, external data movement, spending limits, and access changes |
| **Approval inbox** | Bounded approvals, expiry, rejection, and changed-target checks |
| **Coverage and destinations** | Connector routes, inspection coverage, protected resources, and destination state |
| **Evidence ledger** | Action history, integrity verification, and a tamper demonstration |
| **Recovery and acceptance lab** | Emergency halt, restore drills, and scenario evaluation |

The Gemini model is optional. With <code>GEMINI_API_KEY</code> configured, persona chat can use Gemini for intent parsing. Without it, Holdpoint uses its local semantic intent compiler. The policy and execution pipeline remains deterministic.

## Run locally

### Requirements

- Node.js **22.12 or later** recommended. [Vite 8 supports Node.js 20.19+ or 22.12+.](https://vite.dev/guide/)
- npm

### Start the app

~~~bash
git clone https://github.com/codesnapper1/holdpoint.git
cd holdpoint
npm ci
~~~

For Gemini-backed persona chat, copy the example environment file and set your key:

~~~bash
# macOS / Linux
cp .env.example .env
~~~

~~~powershell
# Windows PowerShell
Copy-Item .env.example .env
~~~

Then start the app:

~~~bash
npm run dev
~~~

Open [http://localhost:3000](http://localhost:3000). The Express server hosts the Vite app and API routes.

Keep <code>.env</code> private. It is excluded from Git; only <code>.env.example</code> belongs in the repository. When Gemini is enabled, chat prompts are sent to Google's Gemini API using the server-side key.

## Run the checks

~~~bash
npm run lint
npm run test:acceptance
npm run test:stress
npm run test:adversarial
~~~

Or run the complete suite with:

~~~bash
npm test
~~~

## Project map

~~~text
src/
  components/   Workspace, policy, approval, evidence, and recovery UI
  engine/       Personas, action contracts, policy, execution, destinations, and scenarios
public/
  avatars/      Persona artwork
  *hero*        Landing-page artwork
server.ts       Express API and Vite development server
test_*.ts       Acceptance, adversarial, and stress-suite runners
~~~

## Scope and safety

The execution engine, destination registry, approval state, and evidence ledger run in local memory and reset when the server restarts. The destination adapters model outcomes; they are not integrations with banking, email, cloud, identity, or repository providers. Policy decisions and test results are prototype behavior, not a production security guarantee.

## License

No license file is included. No reuse license is implied.
