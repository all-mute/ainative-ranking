# ainative-ranking

**RU.** Скилл для агента: оценивает AI-нативность команды по шести векторам розы ветров от 0 до 10 и строит HTML-отчёт с розой. Векторы: бюджеты и flywheel, культура, онтологии, мясо и вкус, автоматизация рутины, агентская инклюзивность. Порядок работы:
1. Параллельные субагенты собирают доказательства: CI, merge queue и история PR, AGENTS.md и скиллы, MCP-конфиги, работа с секретами, доки, бюджеты и гейтвеи.
2. Где доказательств нет, агент задаёт человеку короткий опросник, а не гадает.
3. Каждый балл опирается на якорь рубрики, ссылается на источник и имеет уровень уверенности.
4. Отчёт выделяет слабое звено и говорит, что закрывать первым.

**EN.** An agent skill that scores a team's AI-nativeness 0–10 on a six-vector wind rose (budgets & flywheel, culture, ontologies, meat & taste, routine automation, agent inclusivity):
1. Parallel research subagents gather evidence from the workspace.
2. Where evidence is missing, the agent asks a short questionnaire instead of guessing.
3. It scores each vector against an explicit anchored rubric, with a cited source and a confidence level.
4. It renders a self-contained HTML report: the rose with the weakest ray highlighted, per-vector evidence in collapsibles, and a "what to close first" plan.

## Install

Claude Code (project or user level):

```bash
git clone https://github.com/all-mute/ainative-ranking ~/.claude/skills/ainative-ranking
# or: .claude/skills/ainative-ranking inside a repo
```

Other agents (Codex, Cursor, Gemini CLI, OpenCode): point them at `SKILL.md`. It is plain markdown, and the procedure does not depend on Claude-specific tools. Subagents are optional; without them, the research runs in three passes.

## Use

> «Оцени AI-нативность нашей команды и построй розу»
> "Score this team on the AI-native rose"

Output: `ainative-report.html` plus a scores table in chat.

## Files

| Path | What |
|---|---|
| `SKILL.md` | Procedure: scope → parallel research → questionnaire → score → priority → render |
| `references/rubric.md` | 0–10 anchors per vector, killer metrics, red-flag questions, priority logic |
| `references/research-protocol.md` | Subagent prompts per vector, what to check, JSON return format |
| `templates/report.html` | Self-contained report; the agent substitutes one JSON line |
| `examples/example-scores.json` | Fictional example input |
| `examples/example-report.html` | That example rendered |

## Render manually

```bash
python3 - scores.json templates/report.html ainative-report.html <<'PY'
import json,sys
d=json.load(open(sys.argv[1])); h=open(sys.argv[2],encoding="utf-8").read()
open(sys.argv[3],"w",encoding="utf-8").write(h.replace("/*__SCORES_JSON__*/null",json.dumps(d,ensure_ascii=False)))
PY
```

## Scale

0–3 weak · 4–6 medium · 7–10 top. Artifacts outweigh self-report. Re-score quarterly, because the frontier moves.

License: MIT.
