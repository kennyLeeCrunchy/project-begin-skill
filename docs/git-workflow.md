# Git 管理方法

## 核心原则

- 默认在当前分支继续开发，不主动创建或切换 branch / worktree。
- `main` 作为稳定版本使用时，应尽量保持可运行、可恢复。
- branch 用于用户要求的独立开发、实验或后续单独合并的任务。
- worktree 用于多个任务需要同时修改同一仓库的情况。
- Agent 对话 / Fork 保存的是上下文，不等于 Git 分支，也不是代码备份。

可以简单理解为：

```text
默认开发 → 当前 branch
用户要求独立开发 → 新 branch
用户要求并行开发 → branch + worktree
```

## 日常开发

开始修改前先检查：

```bash
git status
git branch --show-current
```

确认当前分支和已有未提交修改。如果用户没有提出 Git 管理要求，就继续在当前分支完成开发和提交。

不要因为开始一个新功能就自动切换到 `main` 或创建 `feat/xxx`。用户明确要求新分支时再创建：

```bash
git switch -c feat/evidence-review
```

分支按任务或功能划分，不需要机械拆分 frontend / backend。一个功能分支可以同时修改前端、后端、测试和文档。

## 多 Agent 并行

如果 Codex、Claude 或多个任务只是串行工作，不需要额外 worktree。

只有多个写任务需要同时修改同一个仓库时，才为每个任务使用独立 branch + worktree：

```text
Task A → branch A + worktree A
Task B → branch B + worktree B
```

示例：

```bash
git worktree add <worktree-path> -b feat/evidence-review main
git worktree add <another-worktree-path> -b fix/provider-retry main
```

不同 worktree 的文件互不覆盖，但最终合并时仍可能产生冲突。并行任务如果共享 API、数据库 Schema、公共类型或核心状态，应先确定契约再开发。

## 提交与合并

多任务环境下优先暂存本任务明确修改的文件：

```bash
git add path/to/file
```

谨慎使用 `git add .`。不得擅自覆盖、回退、stash 或删除用户及其他任务留下的未提交修改。

只有存在独立 branch 时才需要考虑合并。合并或提交前应检查 diff，并运行与改动相关的测试、lint、typecheck、build 或 migration check。

发生冲突时，应理解双方实现后进行整合，尤其检查 API、数据库模型、公共类型、配置、依赖和启动脚本，不要机械选择全部接受 Current 或 Incoming。

## Worktree 清理

确认分支已经合入并验证后：

```bash
git worktree remove <worktree-path>
git branch -d <branch>
git worktree prune
```

优先使用 Git 命令清理 worktree，不要直接删除 Git 正在管理的目录。

## 多仓库项目

如果根目录下有多个独立 Git 仓库，那么每个仓库分别拥有自己的 branch、commit、status 和 history。跨仓库功能需要分别提交；是否创建新 branch，仍遵循“用户没有要求就继续当前分支”的规则。

一句话规则：默认留在当前 branch；需要隔离时开 branch，需要并行时再加 worktree。

## 本仓库拓扑

- `main` 保存可发布的 skill、模板和配套文档。
- 修改 `SKILL.md`、`agents/openai.yaml` 或 `assets/` 时，应检查 README 中的使用方式、理念说明和仓库结构是否仍然准确。
- 修改 `/docs` 下正式文档时，应同步检查根目录 `AGENTS.md` 的文档导航。
- 本仓库没有运行时构建；文档变更至少执行 `git diff --check`，并确认必需文件仍然存在。
