# 在 ChatGPT 中使用

版本：3.0.0。本文介绍独立技能、插件和会话附件三种使用方式。实际入口取决于账户、工作区权限、产品界面和执行环境。

## 选择适合自己的入口

| 使用方式 | 适用情况 | 使用方法 |
| --- | --- | --- |
| 独立 Skills | 界面提供技能创建、上传或安装入口 | 在 Skills 中创建或上传技能，按界面完成扫描、审查和安装。 |
| 插件 | 界面提供插件目录、工作区导入或本地插件入口 | 安装包含本技能的插件，再在新聊天中选择对应技能或插件。 |
| 会话附件 | 当前聊天可以读取上传文件 | 上传规则文件或发行 ZIP，明确要求读取规则；有 Python 时解压并运行同一门控。 |

OpenAI 当前说明，ChatGPT 工作区 Skills 面向符合条件的 Business、Enterprise、Healthcare 和 Edu 用户，具体可用性受工作区和产品设置影响。官方入口为 **Plugins → Skills → Create → Upload from your computer**；该说明没有为所有 ChatGPT 账户保证同一种 ZIP 导入方式。[Skills in ChatGPT](https://help.openai.com/en/articles/20001066-skills-in-chatgpt)

独立技能和插件有不同的分发与管理方式。官方文档列出 ChatGPT 桌面端的独立技能支持，以及 Chat 和 Work 在网页、桌面和移动端使用插件内技能的支持范围；以当前界面实际提供的能力为准。[Build skills](https://learn.chatgpt.com/docs/build-skills)

## 使用发行包

本仓库包含两种可生成的发行包：

- **单技能 ZIP**：包含一个技能目录及其 `SKILL.md`、参考文件和 Python 门控脚本。
- **插件 ZIP**：根目录包含 `plugin.json` 和 `skills/shekang-academic-writing/`。便携插件从 `skills/` 自动发现技能，根清单不声明 `skills` 字段。

插件 ZIP 的上传入口和权限与独立 Skills 分开管理。支持上传的 ChatGPT 工作区可通过管理员插件页面导入；公开插件目录还需要单独的提交、检查与发布流程。GitHub 源码公开或下载 ZIP 均不代表已上架官方插件目录。[插件使用与 ZIP 上传](https://help.openai.com/en/articles/20001256-plugins-in-chatgpt)、[便携插件结构](https://developers.openai.com/plugins/build/plugins)

安装后，在支持的 ChatGPT 界面输入 `@`，选择 `shekang-academic-writing` 或包含它的插件。ChatGPT 也可能根据任务和技能描述自动选择已安装技能；需要明确指定时使用 `@` 选择器。[技能调用方式](https://learn.chatgpt.com/docs/build-skills#how-chatgpt-and-codex-use-skills)

## 普通聊天与 Work

在聊天中上传文件是一种为本次任务提供资料的方式。先确认聊天能读取附件，再要求读取 [主技能](../skills/shekang-academic-writing/SKILL.md) 和 [门控指南](../skills/shekang-academic-writing/references/anti-defensive-gate.md)。若上传界面支持 ZIP，可上传单技能包或插件包；需要 Python 解压时，让模型定位实际上传文件及解压目录。若界面不接受 ZIP，先在本地解压，再按界面支持的文件类型提供规则和脚本。

具备 Python 执行工具且文件可访问时，ChatGPT 可以运行包内的离线门控。普通聊天的数据分析环境能使用提供给当前会话的文件；云端不会自动读取电脑上的技能安装目录。Work 的本地与云端执行也具有不同的文件和工具范围。[ChatGPT 数据分析](https://help.openai.com/en/articles/8437071-data-analysis-with-chatgpt)、[Work 执行环境](https://learn.chatgpt.com/docs/enterprise/chatgpt-work-overview)

没有 Python 或无法访问脚本时，可以完成材料阅读、表达诊断和审查准备，然后转到有执行能力的环境。此时不能报告脚本 `PASS`，也不能将正文作为已完成强制门控的成品交付。

## 可复制的使用提示词

已安装技能时，先通过 `@` 选择本技能，再发送：

```text
请按 shekang-academic-writing 3.0.0 润色我提供的稿件，只修改语言表达。
先读取主技能和 references/anti-defensive-gate.md。
保留所有信息、数字、负面结果、引文支持范围和结论力度，不增补机制或作者判断。
删去无信息增量的自我削弱、预设反对及机械段尾，保留有科学含义的限定。
将最终正文保存或提取为 UTF-8 文本，运行 writing_gate.py prepare，逐项完成语义审查，再运行 check。
只有与最终正文对应的报告为 PASS 时才交付。若没有 Python 或无法访问脚本，请说明具体缺口，并完成可进行的阅读和审查准备，不声称通过门控。
```

使用附件时发送：

```text
附件是 shekang-academic-writing 3.0.0 技能包和本次稿件。
请先查看附件结构，读取其中的 SKILL.md 与 references/anti-defensive-gate.md。
若具备 Python，请在当前可访问目录解压包，定位技能内 scripts/writing_gate.py，并按 prepare → 语义审查 → check 完成同一门控。
本次只做语言润色，保留全部科学含义、统计判断、数字、引文范围和必要不确定性。
正文修改后重新检查，只在最终文本对应报告为 PASS 时交付。
如果执行环境不足，请说明缺口，不把人工检查当作脚本 PASS。
```

学习样本文风时补充：

```text
请完整阅读主稿和我提供的全部样本，记录阅读覆盖与缺失范围。
只迁移措辞、句式节奏和段落推进方式，形成原创表达；样本不提供新稿的事实、数据、机制或引文。
修改范围仍为语言表达，最终正文按同一门控检查。
```

## 门控如何完成

按 [门控指南](../skills/shekang-academic-writing/references/anti-defensive-gate.md) 从实际技能目录运行脚本。`prepare` 生成候选和待填写的审查记录，成功退出不代表门控通过。逐项审查全文、处理候选后运行 `check`；只有最终文本对应的 `PASS` 才满足本技能的交付要求。正文变化后必须更新记录并重新检查。

脚本使用 Python 标准库，离线检查文本和审查记录。候选扫描用于提示复核；语义审查负责判断限定是否必要、内容与证据是否保持一致。门控通过不代表机器已证明文章没有任何表达或科学问题。

## 自定义 GPT 的使用位置

已有自定义 GPT 可在其权限允许时，将共同规则放入 Instructions，将参考材料作为 Knowledge，并测试所需执行能力。知识文件与行为指令承担不同用途。当前官方建议将自定义 GPT 工作流迁移到插件，且个人账户的新建与发布入口存在限制；因此本项目优先提供标准技能、插件包和会话使用说明。[创建与编辑 GPT](https://help.openai.com/en/articles/8554397-creating-and-editing-gpts)、[迁移到插件](https://learn.chatgpt.com/docs/migrate-custom-gpts)

## 相关文件

- [中文 README](../README.md)
- [Codex 使用说明](codex.md)
- [主技能](../skills/shekang-academic-writing/SKILL.md)
- [门控指南](../skills/shekang-academic-writing/references/anti-defensive-gate.md)

平台说明核实日期：2026-10-13。具体账户的界面与权限需在使用时确认。
