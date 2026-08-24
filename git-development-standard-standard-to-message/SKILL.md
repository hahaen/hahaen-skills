---
name: git-development-standard-standard-to-message
description: Enforce the project's Git workflow for branch naming, file staging, commits, pushes, merges, rebases, and pull requests. Use when the user asks to inspect Git changes, prepare or stage files, create or manage branches, generate commit or PR text, commit, push, merge, rebase, or open a pull request.
---

# Git Development Standard

Apply this workflow to Git-related tasks in every repository. This Skill only inspects changes and generates the final Commit Message plus a Git command for the user. It does not execute Git state-changing commands.

## Operation safety

Classify the requested operation before acting:

- Read-only operations such as `git status`, `git diff`, `git diff --cached`, `git log`, `git branch --show-current`, and `git branch --list` may be used for inspection.
- Do not run `git add`, `git commit`, `git push`, `git merge`, or `git rebase`. The user will execute Git operations themselves.
- Never use `--force`, `--no-verify`, history rewriting, or destructive commands such as `git reset --hard` or `git clean` unless the user explicitly requests that exact operation and its scope is clear.
- Do not overwrite, discard, or stage the user's pre-existing changes without calling them out first.

## Required pre-output confirmation

Before generating the final Commit Message and Git command, inspect the relevant diff and ask all five questions:

1. **Change Type**: Is this a new feature, bug fix, refactor, performance optimization, or other change?
2. **Change Scope**: Which module or business area does the change affect?
3. **Change Description**: What exactly was changed?
4. **Test Status**: Has testing been completed? What was the result?
5. **Output Confirmation**: Do you confirm generating the Commit Message? This confirms output only and does not authorize any Git operation.

Do not treat answers to the first four questions as confirmation. The fifth answer must be an unambiguous confirmation, such as “确认生成”. If any answer is missing or unclear, stop and ask only for the missing information.

After confirmation, perform these checks immediately before generating the output:

1. Confirm the current branch is not `main`.
2. Review both `git diff` and `git diff --cached`.
3. Review the changed file list and identify the exact files belonging to this commit.
4. If unrelated files are already staged, stop and ask the user to resolve the staging scope before generating a command.
5. Run or verify the relevant tests and record the result.
6. Check for credentials, tokens, private keys, local environment files, generated secrets, or other sensitive data.
7. If the staged content differs materially from the confirmed description, stop and ask again.

Only then generate the final Commit Message and Git command. Do not execute `git add`, `git commit`, `git push`, `git merge`, or `git rebase`.

## Branch rules

- Never commit directly to `main`.
- Use one independent branch per requirement.
- Recommend branch names in the format `<type>/<module>-<description>`.
- Valid branch types: `feature`, `fix`, `hotfix`, `refactor`, `release`, `docs`.
- Use lowercase kebab-case for module and description; keep the name concise and specific.

Examples:

- `feature/dashboard-external-link-popup`
- `fix/dashboard-external-link-opening`
- `refactor/user-service`
- `hotfix/payment-error`

Before creating or switching branches, show the recommended branch name and ask for confirmation if the operation changes the working tree or branch state.

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

After the five questions have been answered and the user confirms generation, output exactly these two items and nothing else:

```text
提交信息: <type>(<scope>): <中文描述>
命令  : git add -- <本次修改文件> && git commit -m "<type>(<scope>): <中文描述>"
```

The `Command` must be the complete, copyable command for the user to execute: stage the exact files belonging to this commit with `git add --`, then commit with `git commit -m`. Replace the file placeholders with the actual changed file paths. Quote paths containing spaces. Use the exact generated Commit Message in the command. Do not use `git add .` or `git add -A`. Do not execute the command. Do not output branch names, PR titles, explanations, test summaries, or other Git commands unless the user explicitly asks for them.

The Commit Message itself must follow this format:

```text
<type>(<scope>): <中文描述>
```

This Skill never executes `git add`, `git commit`, `git push`, `git merge`, or `git rebase`. The user is responsible for executing the generated command and performing any later Git operation.

## Standard response flow

For a Git task, follow this order:

1. Inspect the repository status, current branch, and relevant diff.
2. Identify unrelated or pre-existing changes and protect them.
3. Recommend the branch name, intended file scope, and PR title when relevant.
4. Do not stage files or change Git state.
5. Before generating output, ask the five required questions.
6. After explicit confirmation, re-check branch, changed diff, tests, and sensitive information.
7. Output only the final Commit Message and complete copyable `git add -- ... && git commit -m ...` command if all checks pass.
