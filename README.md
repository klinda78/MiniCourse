# MiniCourse Generator Agent Skill

这是一个可独立分发的 MiniCourse Generator v2 Agent Skill。仓库同时提供 Skill 源文件和完整 bundle；bundle 已包含 Windows Generator CLI，不需要另外下载或重新编译 EXE。

## 下载与内容

下载仓库根目录的 `minicourse-generator-agent.bundle.zip` 并解压：

```text
bin/
└─ learning-by-card-generator-cli.exe

minicourse-generator-agent/
├─ SKILL.md
├─ README.md
├─ agents/openai.yaml
└─ references/
   ├─ cli-contract.md
   ├─ examples.md
   ├─ request-schema.md
   └─ result-schema.md
```

其中 CLI 来自提交 `c7f0632` 的 Windows Runner `37133423405`，版本为 `generator-core.v0.6`。

## 工作方式

Skill 是 CLI 的薄适配器：收集并确认学习需求，调用 `plan --protocol v2` 生成完整大纲，等待用户明确确认后生成第一阶段4或5小节课程。后续的课程只有在用户明确要求 Continue 后才会继续生成。当CLI 返回  `course_completed`，全部课程生成完成。

课程 Prompt、Provider 调用、协议编译、资源处理、校验、哈希和 CoursePack ZIP 构建都由 CLI 完成，Skill 不重复实现这些逻辑。

## CLI 与错误处理

Skill 默认使用 bundle 中的 `bin/learning-by-card-generator-cli.exe`，也允许通过 `MINICOURSE_GENERATOR_CLI` 显式指定路径。启动时只调用实际存在的 `version --json`；本版本不要求 `capabilities`、`license status` 或 `provider status` 命令。

CLI 的 JSON 错误对象和退出码会被保留。CLI 缺失、启动失败、空响应或非法 JSON 则由 Agent 报告稳定的传输错误；stderr 不直接回显到对话中。

Provider 凭据由启动 CLI 的安全环境提供，不进入 Skill、命令参数、请求 JSON、Session 或 CoursePack，也不应粘贴到对话中。

完整流程和接口说明见 [`minicourse-generator-agent/README.md`](minicourse-generator-agent/README.md) 与 [`minicourse-generator-agent/references/`](minicourse-generator-agent/references/)。
