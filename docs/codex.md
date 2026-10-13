# 在 Codex 中使用

版本：3.0.0。本技能适用于中英文学术起草、语言润色、样本文风学习及共同表达检查。它保留科学内容与证据力度，并在交付前执行离线 Python 门控。

## 安装独立技能

下载仓库后，在仓库根目录运行：

```shell
python -X utf8 scripts/install_codex.py
```

安装器将仓库中的 `skills/shekang-academic-writing/` 安装到 `CODEX_HOME/skills/shekang-academic-writing/`。未设置 `CODEX_HOME` 时使用当前用户的 `.codex` 目录。更新前完整备份已有技能目录，再覆盖同名公共文件，保留目标中的其他私人扩展。默认备份位于技能根目录的上一级 `backups/shekang-academic-writing/`，每次使用带时间戳的独立目录；阅读安装输出以确认实际目标与备份位置。

安装脚本见 [install_codex.py](../scripts/install_codex.py)。本仓库只携带通用规则；私人样本、作者档案与稿件应自行管理。

Codex 官方也支持从用户、项目、管理员和系统位置发现本地技能，当前文档列出用户级 `.agents/skills/` 及项目级 `.agents/skills/`。本项目安装器采用 `CODEX_HOME/skills` 路线；安装后请在实际宿主中核实技能可见性，必要时通过技能安装器或宿主支持的路径安装同一个自包含目录，避免同名副本造成选择混淆。[本地技能发现位置](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills)

## 调用技能

在支持的 Codex 界面输入 `$` 选择 `shekang-academic-writing`；CLI 或 IDE 扩展也可通过 `/skills` 查看技能。技能修改通常会自动发现，未显示时启动新会话或重启宿主。[技能调用与发现](https://learn.chatgpt.com/docs/build-skills)

可复制的语言润色提示词：

```text
$shekang-academic-writing
请润色我提供的稿件，只修改语言表达。
先读取当前技能的 SKILL.md 和 references/anti-defensive-gate.md。
保留全部信息、数字、统计判断、负面结果、引文支持范围和结论力度，不新增机制或作者判断。
减少无信息增量的自我削弱、预设反对及机械段尾，保留必要限定。
使用作者最新稿作为母版，保存修改稿。
交付前对最终正文执行 prepare → 逐项语义审查 → check，只在最终文本对应报告为 PASS 时交付。
审查记录保存在本次工作的非公开目录，不插入正文。
```

可复制的样本文风提示词：

```text
$shekang-academic-writing
请完整阅读主稿和我提供的全部样本，学习其表达风格，记录阅读覆盖与缺失范围。
只迁移措辞、主语与动词选择、句式节奏、段落推进和收束方式，形成原创表达。
不从样本带入事实、数据、机制、引文或经历，保留主稿全部内容及证据力度。
完成改稿后运行同一门控；正文再改须重新检查。
```

## 运行离线门控

以下命令从仓库根目录运行。将示例正文路径替换为本次最终文本；审查记录保存在稿件的非公开工作目录。

```shell
python -X utf8 skills/shekang-academic-writing/scripts/writing_gate.py prepare --text final.md --review-out private-review/writing-review.json
```

读取候选与全文，完成 [门控指南](../skills/shekang-academic-writing/references/anti-defensive-gate.md) 规定的语义审查。修订正文后重新运行 `prepare`；保留有实质意义的候选时，逐项填写具体理由。实际完成检查后再填写审查状态。

```shell
python -X utf8 skills/shekang-academic-writing/scripts/writing_gate.py check --text final.md --review private-review/writing-review.json --report private-review/writing-report.json
```

`prepare` 成功表示生成审查模板。`check` 退出码为 0 且最终报告为 `PASS`，才满足交付条件。脚本错误、候选未处理、审查缺失或文本已变化时，先修复再检查。没有 Python 时说明具体原因，继续材料阅读与审查准备，转到可执行环境完成检查；不自动把人工检查视为通过。

脚本只使用 Python 标准库，不联网、不修改正文。扫描与审查记录共同构成交付流程，语义判断仍需实际阅读。Word、PDF 和 PPT 先提取最终可见正文，并核对其与交付文件一致；文件排版由相应文档或演示技能负责。

## 将其作为全局写作前置

安装技能不会自动给每个项目增加必用规则。需要全局前置时，可在自己的 Codex home 中编辑 `AGENTS.md`，保留既有要求，并加入：

```text
涉及自然语言成品的起草、改写、润色、翻译或文稿生成时，先读取并执行 shekang-academic-writing 的 SKILL.md 与 references/anti-defensive-gate.md。
其他写作技能和委派写作使用同一共同表达门控；非学术文体保持自身用途和作者语气。
交付前保存或提取最终 UTF-8 正文，运行 prepare，完成逐项语义审查，再运行 check。
只有最终文本对应报告为 PASS 才能交付；正文变化后重新检查。执行环境不足时说明原因，不静默跳过或伪称通过。
```

Codex home 默认是当前用户的 `.codex`，也可由 `CODEX_HOME` 指定。同级 `AGENTS.override.md` 会替代 `AGENTS.md`；更具体的项目指令也参与优先级处理。修改后在新会话中检查实际加载的指令。这属于指令前置与脚本检查流程，不是不可绕过的系统输出拦截器。[AGENTS.md 官方说明](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

## 使用便携插件与生成发行包

仓库根 [plugin.json](../plugin.json) 使用便携 Agent Plugins 格式，版本为 3.0.0；技能从根目录 `skills/` 自动发现，因此不声明 `skills` 字段。插件安装与独立技能安装是不同路线，按宿主提供的插件目录或本地 marketplace 流程操作。[便携插件格式](https://developers.openai.com/plugins/build/plugins)

从仓库根目录生成带日期的插件 ZIP 和单技能 ZIP：

```shell
python -X utf8 scripts/build_release.py --date 2026-10-13
```

发行文件输出到 `dist/`：

- `2026-10-13_shekang-academic-writing-v3.0.0-plugin.zip`
- `2026-10-13_shekang-academic-writing-v3.0.0-skill.zip`

插件 ZIP 包含根清单和 `skills/`；单技能 ZIP 包含自包含技能目录。打包脚本见 [build_release.py](../scripts/build_release.py)。生成 ZIP 和公开 GitHub 仓库均不代表完成官方插件目录上架。

## 相关文件

- [中文 README](../README.md)
- [ChatGPT 使用说明](chatgpt.md)
- [主技能](../skills/shekang-academic-writing/SKILL.md)
- [门控指南](../skills/shekang-academic-writing/references/anti-defensive-gate.md)

平台说明核实日期：2026-10-13。
