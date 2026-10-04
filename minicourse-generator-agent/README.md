# MiniCourse Generator Agent Skill

这是一个面向 Agent 的 MiniCourse Generator v2 薄适配器。它只负责收集学习意图、调用本机 Generator CLI、等待用户确认完整课程大纲、按 Stage 生成课程，并解释 CLI 返回的 JSON 结果。

课程规划 Prompt、Provider 调用、协议编译、图片处理、校验、哈希和 ZIP 构建都由 `learning-by-card-generator-cli.exe` 负责，本 Skill 不重复实现这些逻辑。

## 目录

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

## 使用前提

需要安装 Generator Windows CLI：

```text
learning-by-card-generator-cli.exe
```

Agent 应优先使用显式配置的 CLI 路径；没有配置时使用安装程序提供的固定路径。不要扫描整个磁盘，也不要静默修改系统 `PATH`。

启动时依次检查：

```powershell
learning-by-card-generator-cli.exe version --json
learning-by-card-generator-cli.exe capabilities --json
learning-by-card-generator-cli.exe license status --json
learning-by-card-generator-cli.exe provider status --json
```

## 生成流程

1. 收集并确认四项信息：当前基础、学习目标、知识类型、应用场景。
2. 调用 `plan --protocol v2`，展示完整课程大纲和 Stage 边界。
3. 等待用户明确确认，不能自动进入生成阶段。
4. 调用 `confirm-plan --protocol v2` 和第一阶段的 `generate-stage --protocol v2`。
5. 只有 CLI 返回 `stage_completed` 或 `course_completed` 时，才能返回 CoursePack 路径。
6. 用户明确说“继续”后，读取持久化 Session，再调用 `continue --protocol v2`。

每个 Stage 最多五节课；后续 Stage 必须由用户明确 Continue。重复请求使用新的幂等键，并以持久化 Session 为准，不能依赖对话记忆决定下一阶段。

## 安全边界

- 不向用户索要或在 JSON 中写入 Provider API Key。
- 不把 License 内容、Provider 响应、Prompt、临时图片 URL 或 Session 内部数据写入 CoursePack。
- 不从计划、草稿或未验证 ZIP 声称课程已完成。
- 不执行课程中 `code` 或 `terminal` block 的内容。
- 不修改项目中的冻结 Skill：`minicourse-generator` 和 `minicourse-generator-v2`。

## 分发

完整 Skill 可直接使用本目录；也可以使用仓库根目录提供的 `minicourse-generator-agent.bundle.zip` 进行分发。

详细接口请阅读 `references/` 下的三份契约文件。
