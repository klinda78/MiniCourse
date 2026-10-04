# MiniCourse Generator Agent Skill

这是一个面向 Agent 的 MiniCourse Generator v2 薄适配器。

它负责：

- 收集并确认学习意图；
- 调用本机 Generator CLI；
- 展示完整课程大纲并等待用户确认；
- 按 Stage 生成累计 CoursePack；
- 处理显式 Continue、重试和稳定 JSON 结果。

它不负责课程 Prompt、Provider 调用、协议编译、资源处理、校验、哈希或 ZIP 构建，这些工作全部由 Generator Core CLI 完成。

## 文件

```text
minicourse-generator-agent/
├─ SKILL.md
├─ README.md
├─ agents/openai.yaml
└─ references/
   ├─ cli-contract.md
   ├─ request-schema.md
   └─ result-schema.md
```

仓库根目录的 `minicourse-generator-agent.bundle.zip` 是可直接分发的完整 Skill 包。

## 使用前提

需要安装：

```text
learning-by-card-generator-cli.exe
```

Agent 应使用显式配置的 CLI 路径，或使用安装程序提供的固定路径；不会扫描整个磁盘，也不会静默修改系统 `PATH`。

启动检查：

```powershell
learning-by-card-generator-cli.exe version --json
learning-by-card-generator-cli.exe capabilities --json
learning-by-card-generator-cli.exe license status --json
learning-by-card-generator-cli.exe provider status --json
```

## 生成流程

1. 收集并确认当前基础、学习目标、知识类型和应用场景。
2. 使用 `plan --protocol v2` 生成完整大纲和 Stage 边界。
3. 等待用户明确确认。
4. 使用 `confirm-plan --protocol v2` 和 `generate-stage --protocol v2` 生成第一阶段。
5. 只有 `stage_completed` 或 `course_completed` 才能返回 CoursePack 路径。
6. 用户明确要求继续后，读取 Session 并调用 `continue --protocol v2`。

每个 Stage 最多五节课；后续 Stage 不会自动生成。

## 安全边界

- 不索要或传输 Provider API Key；
- 不把 License、Provider 响应、Prompt、临时 URL 或 Session 内部数据写入 CoursePack；
- 不从计划、草稿或未验证 ZIP 声称完成；
- 不执行课程中的 `code` 或 `terminal` 内容。

详细说明见 [`minicourse-generator-agent/README.md`](minicourse-generator-agent/README.md) 和 `references/`。
