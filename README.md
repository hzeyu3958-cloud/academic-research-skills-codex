# Academic Research Skills for Codex

[![Version](https://img.shields.io/badge/version-v0.1.0-blue)](VERSION)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/license-CC%20BY--NC%204.0-lightgrey)](LICENSE)

Codex-native packaging of the Academic Research Skills suite. This repository
vendors the Claude Code upstream project and exposes it as one Codex skill:

```text
skills/academic-research-suite/
  SKILL.md
  manifest.json
  agents/openai.yaml
  ars/
    deep-research/
    academic-paper/
    academic-paper-reviewer/
    academic-pipeline/
    commands/
    docs/
    shared/
    scripts/
```

The original workflow content is preserved under `ars/`. The four upstream
`SKILL.md` files were renamed to `WORKFLOW.md` so Codex registers only the
single `academic-research-suite` router skill.

## Install

Install from GitHub with Codex's skill installer:

```bash
python "$HOME/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py" \
  --repo hzeyu3958-cloud/academic-research-skills-codex \
  --ref main \
  --path skills/academic-research-suite \
  --method git
```

Open a new Codex conversation after installation and verify with `/skills`.
You should see one skill named `academic-research-suite`.

## Usage

Invoke it explicitly when you want the academic workflow:

```text
Use $academic-research-suite to help me plan a systematic literature review on
AI adoption in higher education quality assurance.
```

Supported workflow routes:

| Workflow | Use when you need |
|---|---|
| `deep-research` | Research question refinement, literature review, systematic review, meta-analysis, fact-checking |
| `academic-paper` | Paper outline, drafting, abstract writing, revision, citation formatting, AI disclosure |
| `academic-paper-reviewer` | Manuscript review, peer review simulation, editorial decisions, re-review |
| `academic-pipeline` | End-to-end research-to-paper workflow with integrity gates, review, revision, and final checks |

### Natural voice revision

The Codex adapter includes a Natural Voice / Low-Template override for user
requests such as "AI味太重," "AI率太高," "AIGC痕迹," "too generic," or
"template-like." It routes to academic-paper revision workflows and uses
`academic-paper/references/human_voice_revision_protocol.md` to reduce generic
pipeline prose, add evidence-specific analysis, and calibrate to the user's own
writing sample when available. It improves writing quality and specificity; it
does not promise detector evasion.

Claude-style aliases are emulated by the router. Use plain aliases if your
Codex client reserves slash commands:

```text
ars-plan my tentative paper topic
ars-lit-review AI in higher education QA
ars-full help me run a complete research-to-paper pipeline
```

## Upstream

Adapted from
[Imbad0202/academic-research-skills](https://github.com/Imbad0202/academic-research-skills),
commit `6da09b3090264bc288fea87dbe1582cbd222fb66`.

This package keeps the upstream CC BY-NC 4.0 license and attribution. The large
showcase PDF outputs are omitted from the Codex package to keep the repository
lighter; the Markdown examples and workflow fixtures remain vendored.
