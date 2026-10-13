# Shekang Academic Writing

Version **3.0.0** · [中文说明](README.md)

A portable skill for academic drafting, language editing, translation, and learning a supplied author's writing style. It supports Chinese and English work in ChatGPT and Codex, preserves scientific meaning, and uses an offline Python review gate before delivery.

## What it does

- Makes established facts clear and concrete; removes repetitive self-undermining language, invented objections, and formulaic defensive endings.
- Preserves study populations, outcomes, comparisons, numbers, statistical judgments, citation scope, negative findings, and the strength of conclusions.
- Keeps meaningful uncertainty, including “may,” “might,” and “suggest.” These are not banned words.
- Learns reusable expression patterns from complete supplied samples without borrowing their facts, mechanisms, citations, or distinctive passages.
- Applies shared expression checks to other writing genres while retaining their purpose and voice.

Language editing does not authorize new mechanisms, interpretations, evidence, or conclusions. The skill does not promise an AI-detector score, a similarity score, or journal acceptance.

## Package layout

```text
plugin.json
skills/
  shekang-academic-writing/
    SKILL.md
    references/
    scripts/writing_gate.py
scripts/
  install_codex.py
  build_release.py
docs/
  chatgpt.md
  codex.md
```

The canonical skill is [skills/shekang-academic-writing](skills/shekang-academic-writing/SKILL.md). Its rules, references, and offline gate are self-contained. The root [plugin.json](plugin.json) uses the portable Agent Plugins format. Skills are discovered from `skills/`; the root manifest does not declare a `skills` field. See OpenAI's [plugin packaging guide](https://developers.openai.com/plugins/build/plugins).

## Codex

From the repository root, run:

```shell
python -X utf8 scripts/install_codex.py
```

The installer targets `CODEX_HOME/skills/shekang-academic-writing/`, using the current user's `.codex` directory when `CODEX_HOME` is unset. It backs up the entire existing skill directory, updates matching public files, and retains other private extensions. The default backup is a timestamped directory under `backups/shekang-academic-writing/`, one level above the skills root. Review its output for the actual installation and backup locations.

Open a new session if needed, type `$` and select `shekang-academic-writing`. Codex CLI and the IDE extension also provide `/skills`. Verify that your host discovers the installed skill; OpenAI documents additional user and repository locations such as `.agents/skills/`. Avoid installing duplicate skills with the same name. See the [Codex guide](docs/codex.md) and [official skill documentation](https://learn.chatgpt.com/docs/build-skills).

Copyable prompt:

```text
$shekang-academic-writing
Edit the supplied manuscript for language only.
Read SKILL.md and references/anti-defensive-gate.md first.
Preserve all information, numbers, negative results, citation scope, and conclusion strength. Add no mechanisms or author judgments.
Remove defensive language only where doing so preserves the meaning and necessary qualifications.
Save the final text as UTF-8, run prepare, complete the semantic review and resolve every candidate, then run check.
Deliver only when the report for the final text is PASS. Recheck after any text change.
Keep review files outside the manuscript and public release.
```

## ChatGPT

Choose the route available to your account and host:

| Route | How to use it |
| --- | --- |
| Standalone Skills | Use the skill creation, upload, or installation interface when available. |
| Plugin | Install a plugin containing this skill through a supported directory, workspace import, or local plugin workflow. |
| Conversation files | Attach the rules or a release ZIP that the uploader accepts. With Python and accessible files, extract the package and run the same gate. |

ChatGPT uses `@` mentions to select an available skill or plugin. Installed skills may also be selected automatically when relevant. Availability varies by account, workspace permissions, and surface; this project does not assume every ChatGPT account can directly import a skill ZIP. See [Skills in ChatGPT](https://help.openai.com/en/articles/20001066-skills-in-chatgpt), [Build skills](https://learn.chatgpt.com/docs/build-skills), and the [ChatGPT guide](docs/chatgpt.md).

Attaching files provides material for the conversation. Cloud execution does not automatically access a local skill installation. ChatGPT's data-analysis environment can run Python using files supplied to the session, and Work has its own local and cloud tool boundaries. See [ChatGPT data analysis](https://help.openai.com/en/articles/8437071-data-analysis-with-chatgpt) and [Work execution environments](https://learn.chatgpt.com/docs/enterprise/chatgpt-work-overview).

Copyable prompt after selecting the installed skill with `@`, or attaching its files:

```text
Use shekang-academic-writing 3.0.0 to edit my manuscript for language only.
Read the supplied SKILL.md and references/anti-defensive-gate.md before writing.
If a ZIP is attached, inspect its structure and locate the skill directory in the current accessible environment.
Preserve scientific meaning, numbers, negative results, statistical judgments, citation scope, and necessary uncertainty.
With Python available, run the packaged writing_gate.py through prepare → semantic review → check.
Deliver only after the report for the final UTF-8 text is PASS. Recheck any later changes.
If Python or the script is unavailable, explain the specific gap and prepare the review; do not claim PASS or treat manual review as completion of the mandatory gate.
```

For new reusable workflows, this project prioritizes skills and plugins. Existing custom GPTs may use the rules where their account and capabilities allow, but they have separate configuration and migration requirements. See OpenAI's [current GPT guidance](https://help.openai.com/en/articles/8554397-creating-and-editing-gpts).

## Mandatory review gate

The gate uses the Python standard library and runs offline. From the repository root:

```shell
python -X utf8 skills/shekang-academic-writing/scripts/writing_gate.py prepare --text final.md --review-out private-review/writing-review.json
```

Read the complete final text and all candidates, revise unnecessary defensive language, and complete the review record as described in the [gate guide](skills/shekang-academic-writing/references/anti-defensive-gate.md). Preparing a record is not a passing result. After a text revision, prepare a matching record again.

```shell
python -X utf8 skills/shekang-academic-writing/scripts/writing_gate.py check --text final.md --review private-review/writing-review.json --report private-review/writing-report.json
```

Delivery requires exit code `0` and a `PASS` report matching the final text. Missing reviews, unresolved candidates, changed text, and execution errors must be resolved before delivery. Without Python, continue reading and review preparation, then complete the gate in an environment that can run it. Manual review does not substitute for script execution.

For Word, PDF, or slides, extract and verify the final visible text before checking. The gate combines rule-based candidate detection with actual semantic review; a pass does not prove that every writing or scientific issue has been eliminated. It is a workflow requirement, not an unbypassable system output interceptor.

## Build a release

```shell
python -X utf8 scripts/build_release.py --date 2026-10-13
```

The build creates two archives in `dist/`:

- `2026-10-13_shekang-academic-writing-v3.0.0-plugin.zip`
- `2026-10-13_shekang-academic-writing-v3.0.0-skill.zip`

The plugin contains the root manifest and `skills/`; the standalone archive contains one self-contained skill directory. Keep private manuscripts, samples, profiles, and review records outside public release files.

Publishing this repository on GitHub makes its source available. Listing it in the official plugin directory requires a separate submission, checks, and publication process. Workspace ZIP upload also has separate eligibility and permissions. See [ChatGPT plugin installation and ZIP upload](https://help.openai.com/en/articles/20001256-plugins-in-chatgpt) and [public plugin submission](https://developers.openai.com/plugins/deploy/submission).

## License and attribution

See [LICENSE](LICENSE) and [ATTRIBUTION.md](ATTRIBUTION.md) for project terms and third-party attribution. The skill adapts Anti-Defensive Writing principles while preserving necessary scientific qualifications and complete reporting.

Platform guidance checked on **2026-10-13**. Confirm the interface and permissions available to your account when installing.
