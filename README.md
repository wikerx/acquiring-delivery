# Acquiring Delivery V2

在 Codex Desktop 用 `$acquiring-delivery` 创建、跟进和审批 Acquiring 研发需求。七个阶段依次为需求分析、技术设计、开发、独立测试、Code Review、最终验收和发布准备。

## 在 Desktop 中使用

1. 检出包含 V2 的交付仓库版本。在 Codex Desktop 添加本地项目，选这个 Git 仓库根目录作为主目录并确认项目可信。Skill 位于 `.agents/skills/`，六个角色位于 `.codex/agents/`。
2. 需要读取业务代码时，附加现有的后端、前端和 SDK 目录。交付项目为主目录时，Skill 会主动读取附加仓库的开发规则。开发阶段用隔离 Worktree，并将写权限限定在批准的范围。Desktop 自带的 PR 和 Worktree 命令以主仓库为目标，业务仓库操作要显式指定目标仓库。
3. 首次在聊天中提供一个单独的本地目录用于私有 Git 台账，或指定已授权且已确认私有的现有 Git 台账，并在 Desktop 中附加所需目录。默认使用一个无远程地址的本地 Git 台账。当前 GitHub 仓库是公开的，真实需求、缺陷、审批和原始测试证据保存在私有台账。具体校验见 [台账协议](.agents/skills/acquiring-delivery/references/ledger.md)。
4. 开始新的交付会话，输入 `$acquiring-delivery 创建需求，我希望……`。旧会话未发现 Skill 或角色时，使用新会话重新加载项目配置。

| 日常操作 | 对话示例 |
|---|---|
| 查状态或恢复 | `$acquiring-delivery 恢复 REQ-014，台账位于我提供的私有目录` |
| 批准当前提审 | `批准 REQ-014/02/r2，进入开发，范围按本轮提审的后端和 Admin` |
| 驳回并限定修订 | `驳回 REQ-014/04/r1，回开发修复 BUG-001，范围仅该缺陷` |
| 记录相应决定 | `$acquiring-delivery 查看 REQ-014 的审批依据和 BUG-001 回归结果` |

Codex 负责创建文件、提交私有台账和恢复状态。阶段审批在聊天中完成。首次使用、项目目录访问和目标 Worktree 权限依 Desktop 当前设置确认。缺少独立角色能力时，Skill 报告阻塞并保留阶段状态。

## 规则与模板

- [阶段、交付物与审批](workflows/requirement-lifecycle.md)
- [平台现行基线与业务规则入口](platform/current-baseline.md)
- [V2 模板索引](templates/README.md)
- [开发准备](workflows/definition-of-ready.md)和[完成标准](workflows/definition-of-done.md)
- [V1 到 V2 的对照以及演练步骤](workflows/desktop-validation.md)

[Codex 项目与文件发现](https://learn.chatgpt.com/docs/projects#use-local-projects-for-folders-and-codebases) · [Codex 自定义 Agent](https://learn.chatgpt.com/docs/agent-configuration/subagents#custom-agents)
