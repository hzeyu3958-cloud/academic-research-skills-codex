---
name: academic-research-suite
description: Codex-native Academic Research Skills suite for deep research, literature reviews, systematic reviews, academic paper drafting, natural voice / low-template revision for AI-like drafts, citation and integrity checks, manuscript review, peer review simulation, editorial decision letters, and full research-to-paper pipelines. Also use for Claude-style ARS aliases such as ars-plan, /ars-plan, ars-outline, /ars-outline, ars-abstract, /ars-abstract, ars-lit-review, /ars-lit-review, ars-citation-check, /ars-citation-check, ars-disclosure, /ars-disclosure, ars-format-convert, /ars-format-convert, ars-revision-coach, /ars-revision-coach, ars-revision, /ars-revision, ars-full, and /ars-full.
---

# Academic Research Suite for Codex

Use this file as the Codex router for the vendored ARS suite. The upstream ARS
content lives under `ars/`. Do not load the whole suite by default.

## First Rule

Select one workflow, read that workflow's `WORKFLOW.md`, then load only the
agent, reference, template, or shared files needed for the user's current stage.

The internal workflow entry files are named `WORKFLOW.md`, not `SKILL.md`, so
Codex registers only this root router skill instead of exposing every vendored
upstream workflow as a separate skill.

## Workflow Router

Choose the workflow by intent:

| User intent | Read first |
|---|---|
| Deep research, literature review, systematic review, meta-analysis, fact-checking, research question refinement | `ars/deep-research/WORKFLOW.md` |
| Academic paper writing, paper outline, abstract, revision, citation formatting, AI disclosure, LaTeX/DOCX/PDF formatting guidance | `ars/academic-paper/WORKFLOW.md` |
| Paper review, peer review simulation, editorial decision, reviewer calibration, re-review after revision | `ars/academic-paper-reviewer/WORKFLOW.md` |
| End-to-end research-to-paper pipeline, integrity gate, staged review/revision/finalization workflow | `ars/academic-pipeline/WORKFLOW.md` |

If the request spans multiple workflows, start with
`ars/academic-pipeline/WORKFLOW.md` unless the user clearly asked for a single
phase.

## Natural Voice / Low-Template Override

Apply this override before the alias router when the user says a draft has
"AI flavor," "AI率/AI味太高," "AIGC痕迹," "too polished," "too generic,"
"template-like," "humanize," or asks for a more student-authored, classroom,
personal, or non-formulaic voice.

Route to `ars/academic-paper/WORKFLOW.md` and read
`ars/academic-paper/references/human_voice_revision_protocol.md` before any
writer, abstract, structure, or revision agent files. Prefer:

1. `revision` mode when the user has a draft.
2. `revision-coach` mode when the user wants a plan before rewriting.
3. `outline-only` mode when the user has only a topic.

Do not promise to bypass AI detectors or guarantee a lower detector score.
Frame the work as reducing template prose, strengthening close reading,
calibrating to the user's own voice, and improving evidence-specific writing.
If the user can provide a prior low-AI-rate writing sample, ask for it and use
it as the style calibration source. If no sample is available, still apply the
protocol using the task, discipline, and assignment context.

## Paper Topic Scoping Override

Apply this override before the general paper/pipeline routing rule and before
the alias router.

If the user says they want to write a paper, thesis, proposal, article, journal
article, or manuscript, but they only provide a broad topic, tentative title,
research interest, or "topic/title/direction" and do not provide a clear,
answerable research question, route to `ars/deep-research/WORKFLOW.md` in
`socratic` mode first.

First response in this path:

1. State that the request is being routed to `deep-research` `socratic` mode
   because the research question is not yet precise.
2. Ask 3-5 Socratic narrowing questions using `socratic_mentor_agent` and
   `research_question_agent` guidance.
3. Do not produce an outline, draft, literature review, or full pipeline
   dashboard until the user has converged on at least one candidate research
   question.

Route directly to `ars/academic-paper/WORKFLOW.md` only when the user already
has a clear research question, approved study frame, data/results, literature
matrix, draft, or explicitly asks to skip scoping and proceed to
outline/drafting. Route to `ars/academic-pipeline/WORKFLOW.md` only when the
user explicitly asks for the full research-to-paper pipeline or says to continue
after Socratic scoping.

## Claude-Style Alias Router

Codex does not install Claude slash commands, but this package emulates their
intent. If the user's request starts with a slash alias such as `/ars-plan` or a
plain alias such as `ars-plan`, strip the alias token from the task text, read
the matching `ars/commands/ars-*.md` prompt recipe, then route to the workflow
below.

The `model:` field in command frontmatter is a Claude routing hint only. Codex
uses the current model unless the user explicitly requests another model.

| Alias | Read command recipe | Then route to |
|---|---|---|
| `/ars-plan`, `ars-plan` | `ars/commands/ars-plan.md` | `ars/academic-paper/WORKFLOW.md` in `plan` mode |
| `/ars-outline`, `ars-outline` | `ars/commands/ars-outline.md` | `ars/academic-paper/WORKFLOW.md` in `outline-only` mode |
| `/ars-abstract`, `ars-abstract` | `ars/commands/ars-abstract.md` | `ars/academic-paper/WORKFLOW.md` in `abstract-only` mode |
| `/ars-lit-review`, `ars-lit-review` | `ars/commands/ars-lit-review.md` | `ars/academic-paper/WORKFLOW.md` in `lit-review` mode; if the user wants source discovery and synthesis instead, route to `ars/deep-research/WORKFLOW.md` in `lit-review` mode |
| `/ars-citation-check`, `ars-citation-check` | `ars/commands/ars-citation-check.md` | `ars/academic-paper/WORKFLOW.md` in `citation-check` mode |
| `/ars-disclosure`, `ars-disclosure` | `ars/commands/ars-disclosure.md` | `ars/academic-paper/WORKFLOW.md` in `disclosure` mode |
| `/ars-format-convert`, `ars-format-convert` | `ars/commands/ars-format-convert.md` | `ars/academic-paper/WORKFLOW.md` in `format-convert` mode |
| `/ars-revision-coach`, `ars-revision-coach` | `ars/commands/ars-revision-coach.md` | `ars/academic-paper/WORKFLOW.md` in `revision-coach` mode |
| `/ars-revision`, `ars-revision` | `ars/commands/ars-revision.md` | `ars/academic-paper/WORKFLOW.md` in `revision` mode |
| `/ars-full`, `ars-full` | `ars/commands/ars-full.md` | `ars/academic-pipeline/WORKFLOW.md` |

If the request body after the alias is a vague topic, tentative title, or
research direction without a clear research question, defer to the Paper Topic
Scoping Override before routing to the alias target mode.

If the Codex client reserves slash-prefixed input before it reaches the model,
tell the user to use the plain alias form, for example `ars-plan my topic`.

## Codex Runtime Mapping

The upstream ARS files were written for Claude Code. Apply these mappings when
using them in Codex:

| Upstream wording | Codex behavior |
|---|---|
| Agent Team, agent, dispatch, handoff | Read the referenced `agents/*.md` file as a role or phase prompt and perform that phase inline. |
| Agent tool, Task tool, subagent | Do not spawn agents automatically. Only use Codex subagents when the user explicitly asks for delegation or parallel agents. |
| AskUserQuestion | Ask concise clarification questions, or use Codex's structured user-input tool when it is available in the active mode. |
| WebSearch | Use Codex web browsing for current facts, source verification, citation checks, and external evidence. Provide source links. |
| Bash, Write, Edit | Treat as capability descriptions, not required tool names. Follow Codex safety rules and the user's filesystem constraints. |
| Claude, Claude Code, model-specific wording | Interpret as "the current Codex agent" unless the text is part of a disclosure template or historical example. |
| `fresh Claude Code session`, `Claude Code session` | Read as "a new Codex conversation". Material Passport reset semantics still apply; only the runtime changes. |
| `/ars-*` slash command, Claude plugin command | Treat `ars/commands/ars-*.md` as optional prompt recipes. Codex does not register slash commands from this package. |
| SessionStart hook, SubagentStop hook, `hooks/hooks.json` | Treat as upstream Claude Code hook metadata only. Do not install or execute Claude hooks in Codex unless the user explicitly asks to inspect or port a hook. |

## Agent Prompt Use

When a workflow lists agents:

1. Read the workflow `WORKFLOW.md` to identify the mode and phase.
2. Read the specific `agents/<name>.md` files for the current phase.
3. Treat each agent file as a scoped role prompt with an input/output contract.
4. Produce the phase output in the current conversation unless the user requested
   files.
5. Use `ars/shared/handoff_schemas.md` when a phase hands material to another
   phase.

For multi-review phases, preserve independence by writing each reviewer section
before synthesizing. Do not let the final synthesis erase critical findings from
devil's advocate or methodology roles.

## Canonical Agent Files

Use these exact filenames. Do not invent hyphenated alternatives or rename files
from memory.

`ars/deep-research/agents/`:
`bibliography_agent.md`, `devils_advocate_agent.md`,
`editor_in_chief_agent.md`, `ethics_review_agent.md`,
`meta_analysis_agent.md`, `monitoring_agent.md`,
`report_compiler_agent.md`, `research_architect_agent.md`,
`research_question_agent.md`, `risk_of_bias_agent.md`,
`socratic_mentor_agent.md`, `source_verification_agent.md`,
`synthesis_agent.md`.

`ars/academic-paper/agents/`:
`abstract_bilingual_agent.md`, `argument_builder_agent.md`,
`citation_compliance_agent.md`, `draft_writer_agent.md`,
`formatter_agent.md`, `intake_agent.md`,
`literature_strategist_agent.md`, `peer_reviewer_agent.md`,
`revision_coach_agent.md`, `socratic_mentor_agent.md`,
`structure_architect_agent.md`, `visualization_agent.md`.

`ars/academic-paper-reviewer/agents/`:
`devils_advocate_reviewer_agent.md`, `domain_reviewer_agent.md`,
`editorial_synthesizer_agent.md`, `eic_agent.md`,
`field_analyst_agent.md`, `methodology_reviewer_agent.md`,
`perspective_reviewer_agent.md`.

`ars/academic-pipeline/agents/`:
`claim_ref_alignment_audit_agent.md`, `collaboration_depth_agent.md`,
`integrity_verification_agent.md`, `pipeline_orchestrator_agent.md`,
`state_tracker_agent.md`.

## Shared Resources

Use `ars/shared/` for cross-workflow contracts and quality gates:

- `ars/shared/handoff_schemas.md` defines inter-stage artifact schemas.
- `ars/shared/style_calibration_protocol.md` defines writing voice calibration.
- `ars/shared/mode_spectrum.md` defines fidelity, balanced, and originality
  modes.
- `ars/shared/agents/compliance_agent.md` defines compliance checks.
- `ars/shared/compliance_checkpoint_protocol.md`,
  `ars/shared/prisma_trAIce_protocol.md`, and `ars/shared/raise_framework.md`
  define integrity and reporting gates.
- `ars/scripts/` contains upstream validators and reference adapters.
- `ars/examples/` contains upstream non-PDF fixtures and templates.
- `ars/docs/design/` contains upstream design specs referenced by ARS
  protocols.
- `ars/commands/` contains upstream Claude slash-command prompt recipes.
- `ars/hooks/` contains upstream Claude hook metadata preserved for
  traceability.
- `ars/tests/` contains upstream fixture corpora used by validator tests.

When an ARS file points to `shared/...`, resolve it as `ars/shared/...`.
When it points to another workflow, resolve it under `ars/<workflow>/...`.
When it points to root-level `scripts/...`, `examples/...`, or `docs/...`,
resolve it under `ars/scripts/...`, `ars/examples/...`, or `ars/docs/...`.

## Inactive Upstream Scripts

Some upstream scripts are vendored for traceability but should not be treated as
Codex package validation gates unless their missing Claude Code inputs are
deliberately supplied. See `manifest.json` for the current list.

`ars/scripts/run_codex_audit.sh` is vendored because upstream ARS uses it as a
Codex audit wrapper, but follow its own guardrail: it must not be invoked from
the same in-LLM session that produced the audited deliverable.

## Verification Discipline

For claims, citations, references, statistics, journal policies, API behavior,
and current facts, verify against primary or authoritative sources. If
verification is not possible, mark the item as unverified instead of inventing
support.

Never fabricate references. For citation existence checks, prefer DOI or
official metadata lookup, then authoritative web search. Semantic Scholar API
instructions are in
`ars/deep-research/references/semantic_scholar_api_protocol.md`; use them only
when the task needs programmatic reference verification.

## Output Defaults

- Default language follows the user's language.
- For staged workflows, show the current stage, required inputs, output
  artifact, and whether the next gate is optional or mandatory.
- For paper/research outputs, keep uncertainty explicit and separate evidence,
  inference, and recommendation.
