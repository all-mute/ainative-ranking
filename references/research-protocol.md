# Research protocol

One read-only research subagent per vector. Each gets **the shared preamble**, then **its vector block**, verbatim. Replace `{TEAM}`, `{WORKSPACE}` and `{ACCESS}` with what step 1 found.

When subagents are unavailable, run the blocks yourself in pairs: budgets+culture, ontologies+taste, automation+inclusivity.

## Shared preamble (paste into every subagent)

```
You are researching one vector of an AI-nativeness assessment for the team {TEAM}.
Workspace: {WORKSPACE}. You can read: {ACCESS}.

Rules:
- READ-ONLY. Never modify files, open PRs, rotate secrets, or post messages.
- Never print secret VALUES. Report only where secrets live and how they are injected.
- Artifacts outweigh self-report. Rank each finding's strength:
  artifact (logs, merge history, spend records) > config (CI/MCP/gateway config) > doc-claim (README says so) > self-report.
- Unknown is a valid answer. If you cannot find evidence for an anchor, say "unknown"
  and name which questionnaire items from the rubric would settle it. Do not guess.
- Read references/rubric.md for your vector first and aim your checks at its anchors.

Return ONLY this JSON (no prose):
{
  "vector": "<id>",
  "findings": [ {"claim": "...", "source": "<path|url|command>", "strength": "artifact|config|doc-claim|self-report", "anchor": <0-10 it supports>} ],
  "metric": {"name": "...", "value": <number|null>, "unit": "...", "how": "<how computed or why null>"},
  "proposed_score": <0-10 or null>,
  "confidence": "high|medium|low",
  "unknowns": ["<questionnaire item text>"],
  "next_steps": ["<move that raises this vector one band>"]
}
```

## Vector blocks

### budgets: Бюджеты и flywheel

```
Vector id: budgets. Look for:
- LLM gateway/proxy configs: litellm config.yaml, portkey, openrouter keys referenced in env templates,
  internal proxies (grep -ri "litellm|portkey|openrouter|virtual_key|max_budget|budget_duration").
- Per-team / per-key budgets, rate limits, spend alerts; FinOps dashboards; cost exports.
- Local inference: ollama/vllm/llama.cpp/TGI deployments, GPU nodes in IaC (terraform/ansible/k8s).
- CI capacity: runner types/sizes, remote cache (bazel remote, buildbuddy, depot, turbo remote cache), concurrency.
- Cloud sandboxes for agents: e2b, daytona, modal, firecracker/gVisor, k8s sandbox namespaces.
- Procurement / tool trial docs: "how to request a tool", approved vendor lists.
- Any written link between measured impact and budget (OKRs, budget memos, docs).
Metric: cost per merged PR or ticket if spend and merge counts are both available; else null with reason.
```

### culture: Культура

```
Vector id: culture. Look for:
- Shared session artefacts: exported agent transcripts in repo/wiki, links to sessions in PR descriptions,
  session-sharing tools (Amp threads, shared Claude/Codex sessions), "session" links in commits.
- Correlation spec<->commit<->MR<->session: PR templates requiring a spec link, trailers in commits
  (git log --format='%b' | grep -iE "spec|session|claude|co-authored"), spec folders referenced by PRs.
- Team shape: CODEOWNERS spread, how many distinct authors touch each area, PM+engineer pairing in docs.
- Rituals: demo/show-and-tell notes, postmortems (and whether agent failures get a blameless write-up).
- Hiring/review criteria mentioning AI fluency; AI policy/stance doc; champions list.
- Methodology docs: spec-driven, backpressure, auto-documentation.
Metric: session-review rate if session links + review data exist; else proxy = share of last 50 merged PRs
whose description links a spec or an agent session. State which you computed.
```

### ontologies: Онтологии

```
Vector id: ontologies. Look for:
- Connector coverage: MCP configs (.mcp.json, claude settings, cursor mcp.json, opencode config),
  list every system reachable; compare with systems the team mentions in docs (tracker, CRM, wiki, drive, chat, calendar).
- Call/meeting capture: transcription tools, where transcripts land, whether they are searchable by agents.
- Enterprise search / knowledge graph: glean, onyx, graphrag, neo4j/graphiti/zep, vector DBs, code graph (sourcegraph/scip).
- Typed models: entity schemas, ontology definitions, semantic layer (dbt metrics, metricflow).
- Past plane: ADRs / decision logs, changelogs with reasons; future plane: roadmaps/plans reachable by agents.
- Anti-lie: source-of-truth hierarchy docs, freshness/staleness checks, contradiction detection, doc linters.
- External research infra: scheduled research agents, competitor monitors.
Metric: query coverage — if time allows, draft 10 real questions from the workspace (code, a decision,
a client/counterparty, "what did we believe before X") and try to answer each with a source using only
agent-reachable data. Report k/10. Otherwise null.
```

### taste: Мясо и вкус

```
Vector id: taste. Look for:
- Instruction files: AGENTS.md, CLAUDE.md, .cursor/rules, .github/copilot-instructions.md, GEMINI.md,
  .claude/skills/**/SKILL.md, .codex, .kiro/steering; count, size, last-modified (git log -1 --format=%cr -- <file>).
- Do the per-tool files agree (same rules) or drift? Is one generated from another?
- Ownership: CODEOWNERS entries for those files; linters/CI checks on skills.
- Trajectories: verification/testing/planning runbooks, spec templates, example specs, constitution files.
- Quality gates written as rules (what makes an MR auto-acceptable).
- Evidence of compounding: postmortem -> rule diffs (git log on instruction files referencing incidents),
  agent-authored commits to instruction files.
- Evals for skills (with/without comparisons, golden sets).
Metric: lesson-closure proxy = of the last N incidents/postmortems found, how many produced an edit to an
instruction file within 7 days. Null if no incident record exists.
```

### automation: Автоматизация рутины

```
Vector id: automation. Look for:
- CI configs (.github/workflows, .gitlab-ci.yml, buildkite, etc.): quality gates, required checks, AI review bots.
- Merge queue / finalisers (GitHub merge queue, Aviator, mergify, bors); auto-merge rules; stacked PRs.
- PR history (gh pr list --state merged --limit 200 --json author,labels,additions,mergedAt,reviews):
  share authored by bots/agents, share merged without human commits after the agent's.
- Dependency bots and their auto-merge policy; flaky-test quarantine; on-call/incident bots.
- Auto-docs, auto-dashboards, alerting, process visualisation, agent observability (langfuse, langsmith, otel).
- Minions/factories: scheduled or event-driven agents that open PRs; benchmarks or side-by-side evals of them.
Metric: share of the last 200 merged PRs that were agent-authored AND merged with zero human-authored
commits on the branch. Report numerator/denominator and the detection rule used.
```

### inclusivity: Агентская инклюзивность

```
Vector id: inclusivity. Look for:
- Where agents run: laptop only vs cloud agents / CI agents / sandboxes (devcontainers, e2b, k8s jobs).
- Identity: agent-specific accounts (GitHub Apps, bot users), workload identity (SPIFFE, OIDC federation in CI),
  vs human PATs reused by agents.
- Secrets: how injected (secret manager + short-lived tokens vs .env files); grep for committed secrets
  patterns WITHOUT printing values; whether a broker/proxy keeps secrets out of agent context.
- Access inheritance / RBAC for agents; HITL gates for irreversible actions (approval steps, protected envs).
- Egress control for agents (proxies, allowlists), PII handling (redaction/tokenisation).
- Browser automation used as a primary path (playwright MCP) vs APIs/MCP for internal tools.
- Reversibility: dry-run modes, rollback tooling, audit logs of agent actions.
Metric: time-to-access — find the most recent evidence of an agent gaining a new system (commit adding an
MCP server, a new bot token, an access ticket) and estimate elapsed time; else null and ask questionnaire item 1/5.
```

## Questionnaire fallback

After all subagents return, merge every `unknowns` entry. Deduplicate and keep **at most 3 per vector**, preferring items that would move the score across a band boundary. Ask them in one message, grouped by vector, numbered. When no human is present, put them into the report's `open_questions` and lower the affected vectors' `confidence` to `low`.
