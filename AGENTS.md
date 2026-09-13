# Agent 工作入口

本文件是 Agent 在本仓库的唯一默认入口。先读本文件，再按需加载其他文档。

## 项目简介

本仓库 `project-begin-skill` 维护 `project-begin` skill：它为 AI coding agent 初始化项目入口文档、plan/state 协作边界和 Git 协作规则。项目服务需要在 AI 开发中保持上下文一致、文档可追溯和多人协作稳定的开发者与 Agent。仓库范围只包含 skill 说明、Agent 配置、可复用模板和协作文档，不包含具体业务应用代码。

## 技术栈与目录职责

- `SKILL.md`：skill 的触发条件、初始化流程、完成门槛与安全约束。
- `agents/openai.yaml`：skill 的展示信息、简述和默认提示。
- `assets/`：生成目标项目文档时使用的三份可复用模板。
- `docs/`：本仓库的 Agent 协作规则和 Git 工作流。
- `README.md`：面向使用者的安装、理念、结构和贡献说明。

本项目没有运行时依赖、构建系统或业务服务；主要产物是 Markdown 和 YAML 文件。

## 文档导航

- `docs/agent-workflow.md`：Agent 协作规则、plan/state 详细规则。通常无需读取，只有用户要求看协作规则、整体方案、当前进度，或需要创建/更新 `plan.md`、`state.md` 时读取。
- `docs/git-workflow.md`：Git 分支、worktree、提交、合并和多 Agent 并行开发规则。

文档导航维护规则：

- `/docs` 里的有效技术文档都应该能从本导航找到。
- 新增、删除或移动 `/docs` 下文档时，必须同步更新本导航。
- `README.md` 属于用户入口，不替代 `AGENTS.md` 中的 Agent 导航。

## 项目约束

- `SKILL.md` 和模板必须保持项目无关，不写入某个具体业务仓库的绝对路径、目录名或产品假设。
- 初始化目标项目时必须保留已有的项目特定规则；只有真实冲突且无法兼容时才请求用户决定。
- 不自动读取、创建或更新目标项目的 `plan.md`、`state.md`，除非用户明确要求或任务本身满足文档中规定的触发条件。
- 不为了让模板“看起来完整”而修改目标项目的产品代码。
- 不提交密钥、令牌或本机路径等敏感信息。

## 工作规则摘要

- 先读本文件，再按任务需要读取 `/docs` 中的详细规则。
- 只在大型 PRD、新开发阶段、跨模块整体变化或用户明确要求时创建/更新 `plan.md`。
- 用户明确要求更新 `state.md` 时，覆盖重写并保持精炼。
- 新增公共组件、工具函数、数据结构、接口格式、第三方依赖或架构调整时，更新目标项目 `/docs` 下对应文档。
- 仅修改内部逻辑、样式、业务文案，或不改变接口和结构的 bugfix，不强制更新目标项目文档。
- 详细 plan/state 规则见 `docs/agent-workflow.md`；详细 Git 规则见 `docs/git-workflow.md`。

## Git 与并行开发

- 默认在当前分支开发；除非用户明确要求，否则不主动创建、切换或删除 branch / worktree。
- `main` 应保持可运行；修改前检查 `git status`，不得覆盖、回退或提交用户及其他任务的未提交改动。
- 分支按任务或功能划分，不按前端、后端或 Agent 划分。
- 多个任务需要并行修改同一仓库时，使用独立 branch + worktree；串行开发不需要 worktree。
- 并行任务涉及共享 API、Schema、公共类型或核心状态时，先确定契约再开发。
- 合并或提交前检查 diff，并运行与改动相关的测试。

## 环境与配置

- 文档格式：Markdown、YAML。
- 验证工具：Git；可使用 `rg`、PowerShell 或等价工具检查文件与链接。
- 没有包管理器、数据库或外部服务配置。
- 所有外部地址、密钥和环境差异配置都不得写死在 skill 或模板中。

## 验证命令

```bash
git diff --check
```

验证时同时确认以下文件存在且未出现本仓库可解析的模板占位符：

```text
AGENTS.md
SKILL.md
agents/openai.yaml
assets/AGENTS.md.template
assets/agent-workflow.md.template
assets/git-workflow.md.template
docs/agent-workflow.md
docs/git-workflow.md
```
