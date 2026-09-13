# project-begin-skill

`project-begin-skill` 是 `project-begin` skill 的开源仓库。它面向 AI coding agent，把项目背景、协作规则、计划/状态边界和 Git 工作流写进仓库，让人和 Agent 在同一套上下文上工作。

仓库名是 `project-begin-skill`，skill 的调用名仍是 `project-begin`，以保持现有安装和调用方式兼容。

它不是脚手架，也不修改业务代码。运行后会在目标项目中建立三个稳定入口：

- `AGENTS.md`：给 Agent 的项目入口，说明项目是什么、约束是什么、文档在哪里、如何验证。
- `docs/agent-workflow.md`：plan/state 的协作规则，控制什么时候需要长期方案，什么时候只做局部修改。
- `docs/git-workflow.md`：分支、worktree、提交、合并和多 Agent 协作规则。

## 为什么需要它

AI 开发的瓶颈经常不是生成代码，而是让 Agent 在正确的项目边界内持续工作。没有明确的入口文档时，Agent 容易反复询问已经确定的事实、误读旧方案、把临时状态当成最终规则，或者为了一个小改动制造大量计划文档。

`project-begin` 用一组轻量、可审查、放在仓库里的文档，把这些上下文固定下来。文档不是代码之外的负担，而是人和 Agent 之间可共享、可版本化的接口。

## 背后的 vibe coding / AI 开发思想

### 1. Vibe coding 不是“凭感觉写代码”

Vibe coding 的优势在于快速把意图变成可运行的东西，但速度只有在上下文稳定时才会转化为生产力。项目入口、边界、验证命令和协作约定越清楚，Agent 越能把精力放在实现和反馈上，而不是猜测项目规则。

### 2. `AGENTS.md` 是项目的入口，不是百科全书

入口文档只回答 Agent 开始工作时最需要知道的问题：项目服务谁、当前范围是什么、目录各自负责什么、有哪些不可违反的约束、正式文档在哪里、如何验证改动。详细规则放到 `docs/`，避免每次任务都把整个项目说明重新塞进上下文。

### 3. 文档采用渐进式披露

先读入口，再按任务读取相关文档；不自动读取 `plan.md` 和 `state.md`。这样既保留了完整的项目记忆，也避免把无关历史和暂时状态变成 Agent 的噪声。上下文应该按需加载，而不是越多越好。

### 4. Plan、State、代码各司其职

- `plan.md` 记录大型 PRD、新阶段、跨模块变化或明确要求的整体方案。
- `state.md` 记录用户明确要求交接时的当前进度、决策和下一步。
- 代码和正式文档记录已经确认的实现与契约。

小需求、配置调整、bugfix 和文档修正不触发 plan churn。计划是阶段性决策，不是每次对话的流水账。

### 5. README、AGENTS、docs 各自负责什么

`README.md` 给人看：项目是什么、怎么安装、怎么运行、怎么验证、如何贡献。`AGENTS.md` 给 Agent 看：项目边界、约束、入口和导航。`docs/` 放详细协作规则、契约和架构说明。用户可见的安装方式、配置或行为变化时更新 README；只改内部实现或 Agent 交接细节时，不重复制造 README 变更。

### 6. AI 可以快，但 Git 仓库要稳

默认在当前分支开发，不主动创建或切换 branch / worktree；用户明确要求独立开发时再开 branch，需要并行修改时再为任务增加独立 worktree。提交前只暂存本任务文件，并运行与改动范围匹配的验证。这样可以在不增加日常操作成本的前提下，保留 Git 历史的可追溯性。

### 7. 人定方向，Agent 执行

Agent 可以检查结构、生成文档、实现改动、运行验证并报告风险；它不应替人发明业务边界，也不应在不确定时悄悄覆盖用户规则。清晰的文档让人保留方向控制，让 Agent 获得足够的执行自由。

## 工作方式

```text
项目证据 → AGENTS.md 入口 → 按需加载 docs → 实现改动 → 运行验证 → 可追溯提交
```

初始化时，skill 会：

1. 轻量检查目标仓库，不读取 `plan.md` 或 `state.md`。
2. 保留现有项目约束，并把它们合并进入口结构。
3. 生成或合并三份标准文档。
4. 确保 `/docs` 下的正式文档都能从 `AGENTS.md` 导航到。
5. 检查模板占位符、文档链接和小改动不触发 plan 更新等完成条件。

## 使用

### 方式一：让 Codex 直接安装（推荐）

在 Codex 对话中调用 `$skill-installer`，并告诉它从这个 GitHub 仓库安装：

```text
$skill-installer
请从 https://github.com/kennyLeeCrunchy/project-begin-skill 安装 project-begin skill。
```

### 方式二：下载 ZIP 放入 skills 目录

下载 [最新 ZIP](https://github.com/kennyLeeCrunchy/project-begin-skill/archive/refs/heads/main.zip)，解压后把 `project-begin-skill-main` 重命名为 `project-begin`，放入你的 Codex skills 目录。

Windows 常见路径是：

```text
C:\Users\<你的用户名>\.codex\skills\project-begin\SKILL.md
```

如果你的 Codex 使用 `.agents\skills` 作为用户 skills 目录，就放到对应的 `.agents\skills\project-begin`。关键是 `SKILL.md` 必须直接位于 `project-begin` 目录下，不能多套一层 `project-begin-skill-main`。

### 方式三：Git clone（适合需要持续更新的人）

```powershell
git clone https://github.com/kennyLeeCrunchy/project-begin-skill.git "$env:USERPROFILE\.codex\skills\project-begin"
```

Codex 通常会自动发现新 skill；如果列表里没有出现，重启 Codex。

### 第一个项目对话怎么用

在项目的第一个对话里，先告诉 Agent 你的想法、目标用户和需求，再让它初始化项目文档：

```text
我想做一个……
目标用户是……
核心需求是……

请先使用 project-begin 初始化项目文档，再开始拆解和实现。
```

如果你的 skills 目录位置不同，将 clone 目标替换为对应路径即可。这样 Agent 能在第一轮就把想法、边界和协作规则落到项目里，再开始写代码。这个 skill 只生成和维护项目文档，不会自动创建或读取目标项目的 `plan.md`、`state.md`，除非用户明确要求。

## 仓库结构

```text
.
├── SKILL.md                         # skill 主说明
├── agents/openai.yaml               # Agent 展示信息与默认提示
├── assets/                          # 可复用的项目启动模板
│   ├── AGENTS.md.template
│   ├── agent-workflow.md.template
│   └── git-workflow.md.template
├── docs/                            # 本仓库自身的协作规则
│   ├── agent-workflow.md
│   └── git-workflow.md
├── AGENTS.md                        # 本仓库的 Agent 入口
├── LICENSE
└── README.md
```

## 开发与验证

本项目是 Markdown/YAML 文档型 skill，没有运行时依赖。修改后至少运行：

```bash
git diff --check
```

并确认 `SKILL.md`、三个模板、`AGENTS.md` 以及 `docs/` 下两份正式文档仍然存在，且入口导航与实际文件一致。

## 贡献

欢迎提交关于文档结构、Agent 协作边界和模板可复用性的改进。请保持模板项目无关、避免引入隐含的产品假设，并在行为变化时同步更新 README 和相关文档。

## License

MIT License，详见 [LICENSE](LICENSE)。
