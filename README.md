# 佘康学术写作 · Shekang Academic Writing

[English](README_EN.md) · [ChatGPT用法](docs/chatgpt.md) · [Codex用法](docs/codex.md)

面向中英文学术论文的写作、润色与文风改造。通过具体陈述、自然论证和逐项内容核对，减少模板化表达、情绪性自我削弱及段尾机械防御，同时保持科学含义。

版本：3.0.0。支持ChatGPT与Codex共用同一套规则和离线Python门控。

## 主要功能

- 学术润色：优化措辞、语法、指代和衔接，保留事实、数字、引用、统计判断和结论力度。
- 文风学习：完整阅读主稿与目标期刊主编、副主编或作者既往文章，提炼有位置依据的风格特征，再原创迁移到当前稿件。
- 防御性写作检查：减少预设反对、无信息增量的自我削弱和段尾套话；必要限定与对应主张自然整合。
- 全文修订：区分语言润色与实质修改，支持框架整理、批注落实和多稿整合。
- 交付门控：检查脚本、审查记录和最终正文绑定，未完成审查或正文已改变时阻止记录通过。

负面结果、不良事件、失败、未知事项和必要不确定性完整保留。may、might、suggest、“可能”“提示”及统计否定不作为禁词。参考文章用于学习表达，不提供新稿的数据或论点。

## 选择使用方式

| 环境 | 使用方式 | 完成强制门控的条件 |
|---|---|---|
| Codex | 安装单技能，使用 `$shekang-academic-writing`；可追加全局前置模板。 | 本地Python可用，正文及脚本可访问。 |
| ChatGPT技能或插件入口 | 按账户及宿主实际提供的界面安装或上传，使用 `@shekang-academic-writing`。 | 当前执行环境能够运行Python并访问包内文件。 |
| ChatGPT普通会话附件 | 上传包和稿件，要求先读取技能及参考文件，再执行同一检查。 | 会话具备Python执行能力；文件已上传或解压到可访问位置。 |

没有Python执行能力时，可以读取规则、分析材料并准备审查；最终正文转到具备执行条件的环境完成门控。人工确认不能替代脚本 `PASS`。不同ChatGPT账户和工作区的安装入口有差异，详见[ChatGPT指南](docs/chatgpt.md)。

## 在Codex安装

下载本仓库源码并进入仓库根目录：

```text
python scripts/install_codex.py
```

默认安装到 `CODEX_HOME/skills`；未设置时使用用户目录下的 `.codex/skills`。可用 `--dest` 指定技能根目录。已有技能目录会先完整备份，公共文件覆盖同名文件，私人扩展保留。安装脚本不自动修改全局指令。

下一回合调用：

```text
$shekang-academic-writing
请润色这篇论文，保持全部科学内容与结论力度，减少机械防御表达，并运行最终交付门控。
```

希望每次写作都执行共同检查时，将[全局写作前置模板](templates/AGENTS.writing-gate.md)追加到自己的Codex全局指令，保留已有内容。详细步骤见[Codex指南](docs/codex.md)。

## 在ChatGPT使用

发行包有两种形式：

- [插件ZIP](dist/2026-10-13_shekang-academic-writing-v3.0.0-plugin.zip)：根目录含 `plugin.json` 和 `skills/`，供支持便携插件导入的宿主使用。
- [单技能ZIP](dist/2026-10-13_shekang-academic-writing-v3.0.0-skill.zip)：独立、自包含的技能目录，供文件上传、技能工具或手动安装使用。

在具备Python执行能力的ChatGPT会话中上传包与稿件后，可以直接使用以下提示词：

```text
请读取附件中的 shekang-academic-writing/SKILL.md 及其引用的门控文件。
先确认你能访问技能资源并执行Python，再阅读全文。
只修改表达，保持事实、数据、引文支持范围、必要不确定性和结论力度。
减少机械段尾防御。完成prepare、逐项语义审查和check后，再交付最终正文。
```

上传插件、安装单技能和在普通会话读取附件是不同路径。公开GitHub仓库不等于已上架官方插件目录；当前账户可用入口见[ChatGPT指南](docs/chatgpt.md)。

## 门控怎样工作

`prepare`生成候选句和未完成的审查模板。实际阅读全文后，完成七项语义检查，并逐项处理候选。`check`核对正文哈希、确认字段和保留理由；报告为 `PASS` 且退出码为0时才交付。

候选扫描用于提示需要检查的表达，不自动改文。科学范围、不确定性、统计或负面事实、明确期刊要求、必要比较和准确引用，都可以在具体说明后保留。正文再次修改会使旧审查记录失效。

`PASS`表示扫描与审查记录满足规则，实际语义判断仍由审查者负责；它不证明文章没有任何表达或科学问题，也不保证AI检测分数、查重率或审稿结果。完整接口见[门控指南](skills/shekang-academic-writing/references/anti-defensive-gate.md)。

## 本地验证与打包

Python脚本仅依赖标准库，离线运行，无需API密钥。

```text
python -B -m unittest discover -s skills/shekang-academic-writing/scripts -p "test_*.py"
python -B -m unittest discover -s scripts -p "test_*.py"
python scripts/build_release.py --date 2026-10-13
```

构建器使用公开文件白名单。真实稿件、私人风格档案、批注和门控运行记录不进入发行包；运行记录应存放在自己的私人工作目录。

## 来源与许可

本项目由She Kang整理和发布，采用[MIT许可](LICENSE)。防御性写作方法参考Adkid-Zephyr的[Anti-Defensive Writing](https://github.com/Adkid-Zephyr/anti-defensive-writing-Skill)，原许可及本地医学写作适配范围见[归属声明](ATTRIBUTION.md)。

平台结构与使用方式依据官方文档：[插件打包](https://developers.openai.com/plugins/build/plugins)、[技能构建](https://learn.chatgpt.com/docs/build-skills)、[Codex全局指令](https://learn.chatgpt.com/docs/agent-configuration/agents-md)。
