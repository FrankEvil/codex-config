# Codex CLI 配置仓库

## 项目概述

本仓库收集了 Codex CLI 的个性化配置与提示词模板，目标是将资深全栈技术专家的工作方式固化为可复用的自动化流程。通过统一的模型参数、受信任项目列表以及丰富的工具集，本仓库帮助团队快速搭建一致、可审计、可扩展的 AI 协作环境。

## 核心特性

- **模型与推理策略统一**：`config.toml` 固定使用 `gpt-5-codex`，并启用高推理强度，保证复杂任务的可靠性。
- **项目信任分级**：针对常用项目配置 `trust_level`，在敏感仓库与普通仓库之间实现差异化的安全策略。
- **多工具协同**：预置 Context7、Sequential Thinking、时间查询、Playwright 等 MCP 服务，覆盖检索、规划、自动化操作等多种场景。
- **粒度化提示词库**：`prompts/` 目录按照任务阶段提供规划、实现、质检、文档等多种模版，支持组合调用。
- **中文工作流**：所有系统提示与输出约束均为中文，保证团队内部交流一致性。

## 目录结构

```
├── AGENTS.md                  # 顶层系统提示词与交互准则
├── config.toml                # Codex CLI 主配置，包含模型、项目、MCP 服务声明
├── internal_storage.json      # Codex CLI 内部持久化状态
└── prompts/                   # 任务提示词模板集合（Markdown + YAML Front Matter）
```

> 建议在提交新文件前运行 `tree` 或 `ls` 确认目录层级保持清晰；若新增子目录，请同步更新本节说明。

## 快速上手

1. **准备环境**
   - 安装最新的 Codex CLI（参考官方文档）。
   - 确保本地具备 Node.js ≥ 18 与 Python ≥ 3.11，以便运行 `npx` 与 `uvx` 命令。
2. **应用配置**
   - 将本仓库克隆到本地，例如 `~/projects/codex-config`。
   - 在 Codex CLI 的配置目录（默认 `~/.config/codex`）创建或更新符号链接，使其指向本仓库的 `config.toml` 与 `prompts/`。
   - 如需要自定义 `projects` 节点，请复制 `config.toml` 并在本地分支修改，避免直接覆盖主干。
3. **验证安装**
   - 运行 `codex doctor` 或执行一次简单对话，确认模型、项目信任与 MCP 服务均已正确加载。
   - 在 Codex CLI 中执行 `list prompts`（若 CLI 支持）确保自定义提示词可被发现。

## 配置详解

### 模型与推理

- `model = "gpt-5-codex"`：指定 Codex CLI 的默认模型。
- `model_reasoning_effort = "high"`：要求更高的思考深度，适合复杂架构或评审任务。

### 项目信任级

- `[projects."…"]`：为常用工程设定 `trust_level = "trusted"`，允许 CLI 在这些仓库执行写操作与高权限命令。
- 新增项目时建议：
  1. 先以 `read-only` 模式试运行，确认自动化流程安全；
  2. 再将 `trust_level` 调整为 `trusted`，并在 README 的“维护建议”中记录原因与风险缓解措施。

### MCP 服务

下表列出默认启用的 MCP 服务及其作用：

| 服务标识                          | 启动命令                                  | 主要用途                       |
| --------------------------------- | ----------------------------------------- | ------------------------------ |
| `context7`                        | `npx -y @upstash/context7-mcp@latest`     | 查询官方文档 / SDK 参数        |
| `sequential-thinking`             | `npx -y @modelcontextprotocol/server-sequential-thinking` | 复杂任务的分步规划            |
| `mcp-server-time`                 | `uvx mcp-server-time --local-timezone=Asia/Shanghai` | 时区转换与时间获取           |
| `mcp-shrimp-task-manager`         | `npx -y mcp-shrimp-task-manager`          | 任务拆解与可视化（中文模版）  |
| `playwright`                      | `npx @playwright/mcp@latest`              | 浏览器自动化与可视化验证       |

> **最佳实践**：所有 MCP 服务均以“最小必要”原则调用，使用前评估网络、隐私与合规风险；若调用失败，优先回退至离线操作，并在交互中说明降级措施。

### 内部存储

- `internal_storage.json` 记录 CLI 的轻量状态，例如模型提示词是否已展示。除非调试，通常无需修改；若需要重置，可在停止 CLI 后删除该文件。

## 提示词模板说明

### 模板结构

- 每个 Markdown 文件以 YAML Front Matter 开头（如 `description` 字段），用于在 CLI 中生成摘要信息。
- 正文包含执行步骤、输入要求与安全边界，所有文本采用中文约束，确保输出一致性。
- 模板遵循“只描述，不执行”的理念：实际的文件编辑或命令执行由 Codex CLI 调度完成。

### 分类总览

| 分类           | 文件示例                                 | 功能说明                                               |
| -------------- | ---------------------------------------- | ------------------------------------------------------ |
| 规划与分析     | `analyze.md`、`tasks.md`、`instruction-reflector.md` | 生成任务分解、校验规范、反思指令一致性                 |
| 设计与文档     | `specify.md`、`insight-documenter.md`、`api-docs.md` | 产出规格说明、知识沉淀、API 文档                       |
| 实施与测试     | `implement.md`、`gen-tests.md`、`optimize.md` | 指导编码实现、测试生成与性能优化                       |
| 质检与评审     | `review-code.md`、`github-pr-reviewer.md`、`commit-msg.md` | 代码评审、PR 审核、提交信息撰写                       |
| 调试与支持     | `debug.md`、`clarify.md`、`check-env.md` | 问题定位、需求澄清、环境检查                           |
| 专用场景       | `kiro-*.md` 系列                         | 针对 Kiro 产品线的特定流程（需求评审、特性设计等）     |
| UI/前端        | `ui-engineer.md`                         | 聚焦界面实现与交互策略                                 |

> **提示**：添加新模板时保持命名一致性（动词-名词），并在 Front Matter 的 `description` 里使用一句话中文概括，方便快速检索。

### 使用建议

- 在 Codex CLI 中调用命令时，优先选择语义最贴近的模板；若存在流程依赖（如先 `/tasks` 后 `/implement`），请严格按模板所述顺序执行。
- 模板强调“安全边界”：若出现读写限制、需要人工审批等提醒，请在实际操作前与团队确认。
- 若某些模板需要特定项目结构（如 `.specify/` 目录），请在调用前确认目标仓库已满足前置条件。

## 维护与扩展指南

- **版本管理**：新增或更新模板后，请在提交信息中说明变更动机、适用场景与风险缓解措施。
- **一致性检查**：对 `AGENTS.md` 的改动会影响所有任务，请保持审慎；建议在合并前进行团队评审。
- **测试流程**：在真实项目中试运行新模板，记录反馈并回写到模板末尾的注意事项或到本 README 的维护章节。
- **多语言需求**：若需支持英文等其他语言，建议建立独立分支或目录，避免混用导致输出风格混乱。

## 常见问题

- **无法启动 MCP 服务？** 检查本地是否安装 `npx`、`uvx` 等命令；若网络受限，请寻求离线替代方案，并在 CLI 中提示用户。
- **提示词输出为英文？** 确认调用时没有覆盖系统提示；必要时重启 CLI，使 `AGENTS.md` 中的中文约束重新生效。
- **项目未列在 `projects` 中？** CLI 将以默认信任级运行，可能限制写操作。根据需要手动将仓库添加到 `config.toml`。

## 后续规划

- 跟随 Codex CLI 版本迭代，持续验证指令兼容性。
- 根据团队反馈扩展 UI、数据工程、运维等领域的模板，并在本 README 更新分类表。
- 探索通过 MCP 管理更多基础设施（如日志查询、工单系统），并确保遵守安全合规原则。

---

欢迎在实际使用中记录经验与改进建议，并通过 Issue 或 Pull Request 反馈，共同完善这套中文友好的 Codex 工作流。
