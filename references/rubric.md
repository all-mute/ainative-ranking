# Rubric: 0–10 anchors per vector

Each vector has anchors for every score, a killer metric, and red-flag questions that serve as the questionnaire fallback. Bands: **0–3 weak · 4–6 medium · 7–10 top**.

Scoring rules:
- A score is justified only by an anchor whose conditions the evidence meets. **All** conditions of the chosen anchor must hold, and every lower anchor must hold too. A team cannot be a 7 on inclusivity while failing the 4.
- Anchors are cumulative: 6 includes everything in 4 and 5.
- On conflicting evidence, take the lower anchor and record the conflict.

---

## 1. Бюджеты и flywheel (budgets & flywheel), 000°

How much the team has in quotas for LLMs, transcription and tools, and whether measurable goals unlock more resources (the flywheel), in a way that is trackable and measurable.

| Score | Anchor |
|---|---|
| 0 | No paid AI tooling; people pay out of pocket or use free tiers. |
| 1 | A few individual subscriptions, approved case by case. |
| 2 | At most one ~$200/month subscription per person, obtained with difficulty. |
| 3 | Subscriptions for everyone, but a fixed annual budget: no goals unlock more, and spend is not broken down by person or tool. |
| 4 | Quotas for LLMs, transcription and tools are effectively unconstrained for daily work; spend is known within 2x. |
| 5 | A shared gateway (OpenRouter, LiteLLM, Portkey, an internal proxy) with per-team or per-key budgets, and access to many models. |
| 6 | Local inference for tasks too slow online. A procurement path exists for measurable investment in external agent infra. A new tool can be trialled on a real repo within days, not weeks. |
| 7 | The flywheel is explicit: at least one budget increase was justified by measured impact (cost per merged PR or ticket, time saved). Cost-per-outcome is tracked. |
| 8 | Expensive, strong CI sized for agent volume (remote build cache or execution, parallel runners), plus cloud sandboxes for agents, so laptop RAM is not the bottleneck. |
| 9 | Own large local inference used for workloads impossible on APIs. A protected budget for experiments that are expected to fail. |
| 10 | Token budget treated as a compensation-level line per engineer. Cost-per-outcome trends down while adoption widens, and that trend drives budget decisions. |

**Killer metric: cost per shipped outcome, quarter over quarter.** Dollars of token and tool spend per merged PR or resolved ticket.
- Weak: no denominator exists, only a lump total.
- Medium: computed, but budgets change yearly regardless of the number.
- Top: trending down, and it explicitly justified the last budget increase.

**Red flags / questionnaire:**
1. What did the team spend on LLM tokens last month, broken down by person or tool? An answer that is off by more than 2x means there is no system.
2. How long from "I want to try a new AI tool" to a paid seat on a real repo?
3. What happens when someone hits their monthly limit: requests fail, or is there an escalation path?
4. Has anyone received more budget because they proved an AI investment paid off?
5. Do you track cost per merged PR or ticket, or only total spend?

---

## 2. Культура (culture), 060°

Whether people share what they do, work without technical roles, and move the discussion from code to sessions, specs, intents and team bottlenecks.

| Score | Anchor |
|---|---|
| 0 | No AI use, or AI use is discouraged. |
| 1 | Everyone has something like Cursor, and nobody shares anything. |
| 2 | Each person researches context, writes taste files and builds agent access alone. No team play. |
| 3 | Occasional ad-hoc sharing (a Slack post, a demo), with no norm or place for it. |
| 4 | Expertise goes into shared markdown (taste, trajectories, verification methods, runbooks) and is maintained. |
| 5 | Team play is the default: small teams or pairs (manager + engineer per business area), or technical roles abolished. |
| 6 | Agent sessions are shared by default (not opt-in), an AI champion exists per team, and AI fluency is a hiring or review criterion. |
| 7 | The discussion has moved from code to sessions, specs and intents. Tooling correlates each session, task, MR, commit and spec. |
| 8 | Joint incident investigation with a blameless, mechanism-based taxonomy for agent failures. Parallel A/B rollouts. Leaders personally work with agents daily. |
| 9 | Methodologies are institutionalised: backpressure-driven and spec-driven development, automatic documentation, agent-to-agent information exchange. |
| 10 | The team works on its own next bottlenecks (verification technology, strong merge queue and verification in prod) and publishes its methods internally. |

**Killer metric: session-review rate.** The share of agent sessions (not PRs) viewed by someone other than the operator within 48 h, by default.
- Weak: under 5%.
- Medium: 5–40%, by personal habit.
- Top: over 70%, default-on and linked to spec, commit or incident.

**Red flags / questionnaire:**
1. Show me the last agent session two of you looked at together. Was that planned or an accident?
2. Does your last headcount or budget request say what AI already tried?
3. Is there someone people informally ask "how do I do this with an agent"?
4. When an agent last broke something: a written blameless account, or a quiet revert?
5. What happened to the last person whose AI experiment visibly failed?

---

## 3. Онтологии (ontologies), 120°

The share of all information the team works with that agents can reach, from code only (0%) to everything that ever existed in people's heads (100%): systems, calls, clients, counterparties, past, future and plans. Plus how truthful it is.

| Score | Anchor |
|---|---|
| 0 | The agent sees only the file in front of it. |
| 1 | The agent has the repository and nothing else. |
| 2 | One or two connectors (for example Jira, Confluence) where truth and lies are mixed. The rest is pasted into the session by hand. |
| 3 | Several connectors, but calls are not transcribed and decisions live in chats and heads. |
| 4 | Agents reach most systems. Calls are transcribed with quality (not the cheapest option) and are searchable. |
| 5 | Close to 100% of systems and 100% of calls. Clients and counterparties are reachable through CRM. Retrieval is permission-aware (source ACLs applied at query time). |
| 6 | A typed model of objects, links and actions, not just a pile of docs. Retrieval can traverse relations (graph or code graph). Metrics have one definition (semantic layer). |
| 7 | The past plane exists: decision records or ADRs, time-aware facts ("what did we believe on date X"). A research infrastructure covers external questions and competitors. |
| 8 | Memory infrastructure: background consolidation ("dreaming" or sleep-time compute), freshness SLAs on indexes, data contracts. |
| 9 | Anti-lie: an explicit source-of-truth hierarchy, contradiction detection, stale-fact invalidation. The future and plans plane is queryable. |
| 10 | Query coverage above 70% on a stratified internal benchmark, including temporal and contradictory questions. Agents flag contested answers instead of confabulating. The ontology extends outward to users and customers. |

**Killer metric: query coverage.** On about 50 real questions (code, decisions, clients, "what did we believe before X"), the share answered correctly with a source and no pasted context.
- Weak: under 30%.
- Medium: 30–70%.
- Top: over 70%, including temporal and conflicting questions.

**Red flags / questionnaire:**
1. Pick a random contract or decision: can the agent answer a question about it, with a source, in 5 minutes, without pasted context?
2. Ask the same question a month apart: do you get two different uncited answers?
3. Is there a system people describe as "don't trust what's there, ask X instead"?
4. When two documents disagree, does anything resolve it?
5. Do meeting outcomes exist as records with reasons, or only in memory?

---

## 4. Мясо и вкус (meat & taste), 180°

Curated epistemological (not ontological) markdown: trajectories of work, verification, testing, planning and process decisions, plus simple things like "how to take leave". This layer forms the working culture of agents and takes many hours of hand-writing.

| Score | Anchor |
|---|---|
| 0 | No agent instruction files at all. |
| 1 | A stray personal CLAUDE.md or rules file, uncurated. |
| 2 | Several personal files per tool (Cursor, Claude, Copilot) that disagree, and nobody owns them. |
| 3 | A repo-level AGENTS.md or CLAUDE.md exists but is stale (months old) or generic. |
| 4 | Curated, shared files cover code writing and verification for the main repos, with an owner and recent edits. |
| 5 | Skills or rules are scoped and discoverable (progressive disclosure, glob or description triggers) and shared across the team generously. Spec-generation skills exist, with shared examples of stylish specs. |
| 6 | Quality gates are written for over 50% automatic MR acceptance. Regression scaffolding exists. Changes to taste files are reviewed (CODEOWNERS or a linter). |
| 7 | A governed hierarchy (constitution → spec → plan → tasks) with hooks that apply rules on save or commit. Fixes routinely become rules (a compound step). |
| 8 | Taste is written for automations and upper-level bottlenecks, not only for coding and review. Skills are evaluated (with and without) before merge. |
| 9 | Agents propose diffs to their own rules after failures, and humans review. Taste extends beyond code (product, design, voice). |
| 10 | Per-skill regression rate is tracked on held-out evals, and harmful skills are pruned. The authoring loop is continuous (Ralph-style) and bridges into automation. |

**Killer metric: lesson-closure rate.** The share of novel failures in 30 days that produced a preventive edit to a curated file within 7 days.
- Weak: under 20%, or not tracked.
- Medium: 20–70%.
- Top: over 70%, with a median under 24 h and some diffs agent-proposed.

**Red flags / questionnaire:**
1. When was CLAUDE.md or AGENTS.md last edited, and by a human or an agent?
2. Do you have separate rule files for different tools, and do they agree?
3. Did your last postmortem change anything in writing? Can you find it in 60 seconds?
4. How many skills were ever compared in a run with versus without them?
5. Could a stranger execute this runbook verbatim if its author left tomorrow?

---

## 5. Автоматизация рутины (routine automation), 240°

MR automation, merge-queue finalisers, documentation coverage, no lies in ontologies, search and visualisation tools, up to end-to-end closure of routine tasks. The top signal is the share of PRs closed end-to-end without humans of any role.

| Score | Anchor |
|---|---|
| 0 | No CI. |
| 1 | Basic CI (build and test) and nothing else. |
| 2 | CI plus linters. Merging, docs and alerts are manual. |
| 3 | Some bots (dependency updates, formatting), but no quality gates that could admit an agent PR. |
| 4 | 100% of code is covered by specs in a strong team tool, and all code gets additional AI review. |
| 5 | Thin and thick quality gates drive a fully automatic merge review, with a merge queue and finalisers. |
| 6 | Token economics and budgets per agent. A task pool for agents. Auto-updating docs, auto-generated dashboards and alerts, process visualisation, AgentOps. A live number for the share of agent-authored PRs. |
| 7 | Minions are benchmarked or compared side by side, not vibe-built. On-call agents triage before humans are paged. Flaky tests are quarantined automatically. |
| 8 | Taste files for "automation of automation". Graph loops get auto-visualised. An event system shows what cloud agents are doing. External expertise and past mistakes are recorded so they are not repeated. |
| 9 | Code-factory or dark-factory systems close whole task classes (greenfield, legacy rewrite, security). Over 50% of merged PRs are agent-authored and merged without human edits. The same discipline covers non-engineering roles. |
| 10 | 99%+ of PRs are closed end-to-end without involving humans of any role (engineers, managers, testers), with verification capacity scaled to match. |

**Killer metric:** the share of all merged PRs, org-wide, that were agent-authored and merged with zero human edits, on a live dashboard.
- Weak: under 5%, or not producible.
- Medium: 5–30%.
- Top: over 50%, with review-acceptance tracking as an honesty check.

**Red flags / questionnaire:**
1. Show last week's flaky tests: does each have an owner?
2. When an alert fires at 3 am, does anything look at it before a human?
3. What share of this month's PRs were agent-authored? More than 5 minutes to answer means the vector is unmeasured.
4. Do dependency bumps auto-merge under any condition?
5. Have you measured whether the AI reviewer's comments are accepted or ignored?

---

## 6. Агентская инклюзивность (agent inclusivity), 300°

The agent starts like a person with limited mobility. Care for it: integrate it into all systems, design access inheritance or an alternative role model, make it tolerant to errors so it can fail safely and verify its work, handle security, and keep a human in the loop for critical actions.

| Score | Anchor |
|---|---|
| 0 | The agent is not allowed to touch anything beyond autocomplete. |
| 1 | The agent runs locally on a laptop with no VM of its own and no connectors. |
| 2 | One or two connectors (for example Jira, Confluence, which hold both truth and lies). Other systems are reached through Playwright. |
| 3 | Several connectors, but at least one place still requires copying info into the session by hand, or access takes more than 5 minutes. By the 6L rule, that is weak. |
| 4 | Near-full coverage with good navigation. Missing percentages are closed through team Playwright trajectories. |
| 5 | Access inheritance is designed. Cloud agents and service minions obtain access themselves. Over-privileged agents run only in strongly isolated environments. |
| 6 | Credentials are workload identities that expire by themselves. Agents never see raw secrets (broker). Egress is default-deny. Agents have their own accounts in SaaS (GitHub App, bot users). |
| 7 | A role-based access model holds both locally and in the cloud, including secrets, PII and sensitive data. HITL is applied only to critical, irreversible actions. |
| 8 | No single session holds private data, untrusted input and an external channel at once (lethal trifecta). Reversibility is designed (dry-run, rollback). Agent actions are audited. |
| 9 | Identity is attested across multi-agent chains back to the originating human. Authorisation is evaluated per call (ReBAC/ABAC) instead of static RBAC. |
| 10 | Graph-based search finds related context fast, with an anti-lie layer, so rich ontologies do not poison context. Median time-to-access is under 5 minutes with no human click. |

**Killer metric: median time-to-access.** From "the agent needs a system it cannot reach" to its first successful authenticated call.
- Weak: over 1 h, or credentials pasted by hand.
- Medium: minutes, with a human approving.
- Top: under 5 minutes with no human click, zero standing privilege, and break-glass under 0.1%.

**Red flags / questionnaire:**
1. Is any of the agent's current credentials a long-lived key typed into a `.env`?
2. If you revoke an employee's Jira access today, does an agent keep it tomorrow?
3. Who approved the last agent action on prod or client data, and can it be undone?
4. Does any agent transcript contain a raw password or API key?
5. When an agent needs a new internal tool, do you write Playwright or does the tool already have an API or MCP?

---

## Priority logic

- Default macro and micro order: **культура → бюджеты и стратегия → онтологии → автоматизация и вкус (parallel)**. Inclusivity follows once access work is unblocked by budgets.
- Weakest-link rule (Goldratt's Theory of Constraints, Liebig's law of the minimum): fix the vector that currently vetoes the ROI of the others, not the easiest one and not the one already funded.
- Self-report inflates. Per Dan Shapiro's five levels, about 90% of self-described AI-native developers are at level 2. Weight artifacts over interviews.
