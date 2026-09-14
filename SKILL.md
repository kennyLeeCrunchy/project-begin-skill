---
name: project-begin
description: Initialize or repair a project's Agent docs and collaboration workflow once at project setup. Use when the user says project-begin, asks to initialize or repair Agent project docs, or wants the standard plan/state workflow installed; do not use it as a daily task router.
metadata:
  short-description: Bootstrap project Agent docs
---

# Project Begin

Initialize a project's Agent entrypoint and reusable collaboration workflow without discarding project-specific rules.

This is a project-setup and explicit documentation-repair workflow. It is not a daily task router: after the project documents exist, ordinary task routing and Git decisions follow `AGENTS.md` and the linked `/docs` rules.

## Required outputs

Every successful run leaves all three files present and internally consistent:

- `AGENTS.md`: a concise, project-specific entrypoint with no inferable template placeholders left unresolved.
- `docs/agent-workflow.md`: the reusable plan/state workflow, linked from `AGENTS.md`.
- `docs/git-workflow.md`: reusable Git branch, worktree, commit, merge, and multi-agent collaboration rules, linked from `AGENTS.md`.

Use `assets/AGENTS.md.template` as the entrypoint structure, `assets/agent-workflow.md.template` as the plan/state workflow source, and `assets/git-workflow.md.template` as the Git collaboration source.

## README 管理

`README.md` 是面向人的项目说明和上手入口，与面向 Agent 的 `AGENTS.md`、详细协作规则 `/docs` 分工不同。

- 初始化前读取现有 `README.md`，保留其中的项目事实和项目特定规则。
- README 记录项目定位、安装/运行/验证方式、主要目录、配置、使用方式、贡献和许可证等人类需要的信息。
- 当安装方式、用户可见行为、配置或项目范围发生变化时更新 README；仅内部实现、局部 bugfix 或 Agent 协作细节不重复写入 README。
- 不把 `plan.md`、`state.md` 的交接流水账或完整 Agent 规则复制到 README；README 可以链接到 `AGENTS.md` 和 `/docs`。
- README 缺失时，只有在用户要求或项目需要公开上手入口时才新建；skill 的普通初始化不强制创建 README。

## Workflow

1. Treat the current working directory as the target unless the user gives another path.
2. Inspect the repository lightly before writing: directory names, package/config files, runtime files, existing `README.md`, other docs, and existing `AGENTS.md`, `docs/agent-workflow.md`, and `docs/git-workflow.md`. Do not read `plan.md` or `state.md` unless the user separately authorizes it under the repository rules.
3. Read all three template assets completely before deciding changes.
4. Preserve existing project-specific rules. When the user explicitly asks to initialize or repair the project, merge non-conflicting existing content into the template structure. Ask only when a real conflict would require dropping or materially changing a user-authored rule.
5. Fill project-specific sections from repository evidence:
   - state what the project does, its current scope boundary, and who or what it serves;
   - identify actual runtime, dependency management, key directories, configuration files, and validation commands;
   - record known safety, architecture, and integration constraints.
   Do not leave a placeholder when the answer is discoverable. If a fact is genuinely unknown, write a clear “not yet defined” statement instead of bracketed template text.
6. Create or merge `docs/agent-workflow.md`. Keep the reusable plan/state rules intact unless the repository already has stricter compatible rules. The installed workflow must reserve `plan.md` creation or updates for major PRDs, new product phases, material cross-module changes, or an explicit user request; small follow-ups must not trigger plan churn.
7. Create or merge `docs/git-workflow.md`. Keep its reusable rules: stay on the current branch by default, create a branch only when the user requests independent work, add a worktree only for concurrent edits, explicitly handle uncommitted changes before branch/worktree creation, define shared contracts before parallel work, and use scoped staging, safe conflict resolution, validation, and cleanup. Add project-specific repository topology only to the installed project document, never to the reusable template.
8. Ensure every official `/docs` document is represented in the `AGENTS.md` navigation, including `docs/agent-workflow.md` and `docs/git-workflow.md`.
9. If the project has a README, keep its human-facing usage and documentation links consistent; do not create README churn for an internal-only change.
10. Validate the result before reporting completion.

## Completion gate

Do not report success until all checks pass:

- `AGENTS.md` exists and contains a concrete project introduction.
- `docs/agent-workflow.md` exists.
- `docs/git-workflow.md` exists.
- `AGENTS.md` links to `docs/agent-workflow.md`, `docs/git-workflow.md`, and all current official docs.
- No bracketed template placeholder remains when repository evidence can resolve it.
- Existing project-specific constraints are preserved.
- The workflow does not treat small requirements, config tweaks, bugfixes, or documentation edits as plan-update triggers.
- The final report lists files created, merged, or left unchanged.

If any required output cannot be written, report the initialization as incomplete rather than silently substituting an “equivalent” setup.

## Constraints

- Keep `AGENTS.md` concise; put long workflow rules, contracts, API details, and architecture notes under `/docs`.
- Keep Git templates project-agnostic: do not include repository names, absolute paths, product-specific directory layouts, or assumptions about a particular hosting provider.
- Do not delete `plan.md`, `state.md`, or existing documentation.
- This skill installs plan/state rules but does not itself create, read, or update `plan.md` or `state.md` unless the user separately requests it.
- Do not expose secrets while inspecting environment configuration.
- Do not modify product code merely to make the documentation template look complete.
