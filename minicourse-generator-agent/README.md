# MiniCourse Generator Agent Skill

这是 Generator v2 的薄 Agent 适配器，完整 bundle 同时包含 Skill 和已通过 Windows Runner 的 CLI。

## Bundle 内容

```text
bin/
└─ learning-by-card-generator-cli.exe

minicourse-generator-agent/
├─ SKILL.md
├─ README.md
├─ agents/openai.yaml
└─ references/
   ├─ cli-contract.md
   ├─ request-schema.md
   └─ result-schema.md
```

CLI 来源：提交 `c7f0632`，Windows Runner `37133423405`，版本 `generator-core.v0.6`。

## 使用方式

Skill 首先调用 `version --json`，随后所有课程生产命令都显式使用 `--protocol v2`。它先生成完整大纲并等待确认，只生成当前 Stage；后续 Stage 必须由用户明确要求 `Continue`。

Skill 直接调用 bundle 中的 CLI，保留它的 JSON 错误和退出码。CLI 缺失、启动失败、空响应或非法 JSON 由 Agent 统一报告，stderr 不直接返回到对话中。

Provider 密钥不进入 Skill、参数、请求 JSON、Session 或 CoursePack。此 CLI 版本从启动进程已有的环境变量读取 Provider 凭据；缺少凭据时会返回错误，Skill 不会要求用户把密钥粘贴到对话中。

## 当前边界

本 bundle 使用已验证的 c7f0632 CLI，不新增 `capabilities`、`license status` 或 `provider status` 命令，也不重新运行 Windows Runner。
