---
name: git-development-standard
description: Enforce the project's Git workflow for branch naming, file staging, commits, pushes, merges, rebases, and pull requests. Use when the user asks to inspect Git changes, prepare or stage files, create or manage branches, generate commit or PR text, commit, push, merge, rebase, or open a pull request.
---

# Git Development Standard

Apply this workflow to Git-related tasks in every repository. This Skill only inspects changes and directly generates the final Commit Message. It does not execute Git state-changing commands.

## Response language

Always answer the user in Chinese. The final response must be in Chinese; when outputting a Commit Message, keep the required `<type>(<scope>): <中文描述>` format.

## Operation safety

Classify the requested operation before acting:

- Read-only operations such as `git status`, `git diff`, `git diff --cached`, `git log`, `git branch --show-current`, and `git branch --list` may be used for inspection.
- Do not run `git add`, `git commit`, `git push`, `git merge`, or `git rebase`. The user will execute Git operations themselves.
- Never use `--force`, `--no-verify`, history rewriting, or destructive commands such as `git reset --hard` or `git clean` unless the user explicitly requests that exact operation and its scope is clear.
- Do not overwrite, discard, or stage the user's pre-existing changes without calling them out first.

## Direct output rules

Inspect the relevant `git diff` and `git diff --cached`, infer the change type, scope, and concise Chinese description, then output the Commit Message immediately. Do not verify the current branch, ask confirmation questions, require test-status answers, or wait for user input. Do not execute `git add`, `git commit`, `git push`, `git merge`, or `git rebase`.

## Branch rules

- Use one independent branch per requirement when branch operations are requested.
- Recommend branch names in the format `<type>/<module>-<description>`.
- Valid branch types: `feature`, `fix`, `hotfix`, `refactor`, `release`, `docs`.
- Use lowercase kebab-case for module and description; keep the name concise and specific.

Examples:

- `feature/dashboard-external-link-popup`
- `fix/dashboard-external-link-opening`
- `refactor/user-service`
- `hotfix/payment-error`

When branch creation or switching is explicitly requested, recommend a suitable branch name, but do not ask for confirmation.

## Commit and PR rules

Use this format for commit messages and PR titles:

```text
<type>(<scope>): <中文描述>
```

Valid commit types:

- `feat`: 新功能
- `fix`: Bug 修复
- `refactor`: 代码重构
- `perf`: 性能优化
- `style`: 代码格式调整
- `docs`: 文档修改
- `test`: 测试代码
- `build`: 构建或依赖修改
- `ci`: CI/CD 配置
- `chore`: 项目维护
- `revert`: 回滚提交

Keep the description clear, concise, and accurate. One commit should preferably represent one logical change.

Output exactly one item and nothing else:

```text
<type>(<scope>): <中文描述>
```

This Skill never executes `git add`, `git commit`, `git push`, `git merge`, or `git rebase`.

## Standard response flow

For a Git task, follow this order:

1. Inspect the repository status and relevant diff.
2. Identify unrelated or pre-existing changes and protect them.
3. Infer the type, scope, and concise Chinese description from the changes.
4. Do not stage files or change Git state.
5. Output only `<type>(<scope>): <中文描述>`.
