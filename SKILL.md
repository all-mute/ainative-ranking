---
name: ainative-ranking
description: Score a team's AI-nativeness on the six-vector "wind rose" (бюджеты и flywheel, культура, онтологии, мясо и вкус, автоматизация рутины, агентская инклюзивность) from 0 to 10 using evidence gathered by parallel research subagents, then render a self-contained HTML report with the rose, per-vector evidence and a "what to close first" plan. Use when asked to "оценить AI-нативность команды", "построить розу", "ai-native ranking", "assess our AI maturity", "где мы на розе", or to audit how agent-ready a team, repo or organisation is.
---

# AI-native ranking (роза AI-нативности)

Scores one **team** (not a whole company, not its product) on six vectors, 0–10 each, and renders `ainative-report.html`.

The model behind it: every vector is interconnected. If one ray is short, the others cannot deliver frontier results, so the report always leads with the **weakest ray**.

| # | Vector | Bearing | One-line definition |
|---|---|---|---|
| 1 | Бюджеты и flywheel | 000° | quotas for LLMs, transcription and tools, plus measurable goals that unlock more resources |
| 2 | Культура | 060° | sharing, no technical roles, discussion moves from code to sessions, specs, intents and bottlenecks |
| 3 | Онтологии | 120° | share of all information (code, systems, calls, clients, past/future/plans) agents can reach, and how truthful it is |
| 4 | Мясо и вкус | 180° | curated epistemological markdown: trajectories of work, verification and planning; skills; runbooks |
| 5 | Автоматизация рутины | 240° | CI, quality gates, auto-merge, auto-docs, AgentOps, minions, % of PRs closed end-to-end without humans |
| 6 | Агентская инклюзивность | 300° | the agent as a person with limited mobility: identity, access inheritance, secrets, PII, sandboxes, HITL |

Bands: **0–3 weak (слабая) · 4–6 medium (средняя) · 7–10 top (сильная)**.

## Procedure

Follow these steps in order. Do not skip step 2: a score without evidence is a guess.

### 1. Scope (2 minutes)

- Name the team being scored and its workspace: repo(s), org, tracker, chat. When the user gave none, use the current working directory and git remote.
- Record what you can read: git, `gh`/`glab`, CI configs, docs, MCP config. Record what you cannot: finance, HR, meetings.
- Read `references/rubric.md` in full. Every score must map to an anchor there.

### 2. Research in parallel

Dispatch **one research subagent per vector**. When subagents are unavailable or budget is tight, use three pairs instead: budgets+culture, ontologies+taste, automation+inclusivity. Each subagent gets its prompt from `references/research-protocol.md`: the checks to run, the files to look for, and the return format. They run concurrently and are read-only: they never change the repo, rotate secrets or open PRs.

Rules every subagent follows:

- **Artifacts outweigh self-report.** Merge logs, CI configs, commit authorship, spend records and config files count. Interviews and README claims count only as leads.
- Each finding carries a `source` (a path, URL, command and its output, or "asked human") and a `strength`: `artifact` / `config` / `doc-claim` / `self-report`.
- Unknown is a valid answer. A subagent that cannot find evidence returns `unknown` plus which red-flag questions would settle it. It never infers a score from silence.

### 3. Fill the gaps with the human

Collect every `unknown` into **one short questionnaire**: at most 3 questions per vector, taken from the red-flag lists in `references/rubric.md`. Ask it in one message.

- When the human answers, record the answers with `strength: self-report`.
- When nobody can answer (non-interactive run), score the vector from artifacts alone, set `confidence: low`, and list the open questions in the report.

### 4. Score

For each vector:

1. Place it in a band (weak / medium / top) by matching the evidence to the band's anchors in `references/rubric.md`.
2. Pick the exact score inside the band using the per-point anchors.
3. Compute the vector's **killer metric** when the data allows, and state it with its value and threshold.
4. Set `confidence`:
   - `high`: two or more independent artifacts agree;
   - `medium`: one artifact, or artifacts plus self-report;
   - `low`: self-report only, or mostly unknown.
5. Write 1–3 `next_steps`: concrete moves that would raise the score by one band.

Never round a score up because a vector "feels" advanced. When evidence conflicts, take the lower anchor and say why.

### 5. Decide what to close first

- The **weakest ray** is the lowest score. On a tie, it is the vector that comes earlier in the default priority order: **культура → бюджеты и flywheel → онтологии → автоматизация рутины и мясо и вкус (in parallel) → агентская инклюзивность**.
- Build `priority` as an ordered list: weakest ray first, then the remaining vectors below 7 in the default order.
- Explain in one sentence per item why it vetoes the others (weakest-link logic: nine out of ten on five rays buys nothing when the sixth is two).

### 6. Render the report

1. Write the result as JSON matching `examples/example-scores.json`, with the same keys and six vectors in the order above.
2. Copy `templates/report.html` to the output path (default `./ainative-report.html`) and replace the single line `/*__SCORES_JSON__*/null` with the JSON object.

   The page is self-contained (no network needed except optional Google Fonts) and renders:
   - the rose over weak/medium/top rings, with the weakest ray highlighted;
   - per-vector evidence in collapsibles;
   - the killer metrics;
   - the questionnaire leftovers;
   - the "what to close first" list.
3. Do **not** hand-write HTML. When you need a layout change, edit the template.

A one-liner that does the substitution (Python 3, no dependencies):

```bash
python3 - "$SCORES" templates/report.html ainative-report.html <<'PY'
import json,sys
data=json.load(open(sys.argv[1]))
html=open(sys.argv[2],encoding="utf-8").read()
assert "/*__SCORES_JSON__*/null" in html
open(sys.argv[3],"w",encoding="utf-8").write(html.replace("/*__SCORES_JSON__*/null",json.dumps(data,ensure_ascii=False)))
PY
```

### 7. Hand over

In chat, give:
- the six scores as a table;
- the weakest ray;
- the top three next steps;
- the path to the HTML.

State how many scores are `low` confidence and which questions remain open. Recommend re-scoring every quarter, because the frontier moves outward.

## Guardrails

- The skill scores **the team**. When the user's real question is about product AI-nativeness, say that this rose does not measure it.
- Do not read secret values. Checking that secrets exist, how they are injected and whether they appear in transcripts or `.env` files is enough. Report locations, never contents.
- Do not publish the report anywhere unless asked. It describes an organisation's internals.
- Never invent a number. A metric you could not compute is `null` with a reason.
