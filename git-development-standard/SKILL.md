---
name: git-development-standard
description: Enforce the project's Git workflow for branch naming, file staging, commits, pushes, merges, rebases, and pull requests. Use when the user asks to inspect Git changes, prepare or stage files, create or manage branches, generate commit or PR text, commit, push, merge, rebase, or open a pull request.
---

# Git Development Standard

Apply this workflow to Git-related tasks in every repository. This Skill only inspects changes and generates the final Commit Message for the user. It does not execute Git state-changing commands.

## Operation safety

Classify the requested operation before acting:

- Read-only operations such as `git status`, `git diff`, `git diff --cached`, `git log`, `git branch --show-current`, and `git branch --list` may be used for inspection.
- Do not run `git add`, `git commit`, `git push`, `git merge`, or `git rebase`. The user will execute Git operations themselves.
- Never use `--force`, `--no-verify`, history rewriting, or destructive commands such as `git reset --hard` or `git clean` unless the user explicitly requests that exact operation and its scope is clear.
- Do not overwrite, discard, or stage the user's pre-existing changes without calling them out first.

## Required pre-output confirmation

Before generating the final Commit Message, inspect the relevant diff and ask all five questions:

1. **Change Type**: Is this a new feature, bug fix, refactor, performance optimization, or other change?
2. **Change Scope**: Which module or business area does the change affect?
3. **Change Description**: What exactly was changed?
4. **Test Status**: Has testing been completed? What was the result?
5. **Output Confirmation**: Do you confirm generating the Commit Message? This confirms output only and does not authorize any Git operation.

Do not treat answers to the first four questions as confirmation. The fifth answer must be an unambiguous confirmation, such as “确认生成”. If any answer is missing or unclear, stop and ask only for the missing information.

After confirmation, perform these checks immediately before generating the output:

1. Confirm the current branch is not `main`.
2. Review both `git diff` and `git diff --cached`.
3. Review the changed file list and, if a staging area already exists, confirm the staged file list contains only the intended files.
4. Run or verify the relevant tests and record the result.
5. Check for credentials, tokens, private keys, local environment files, generated secrets, or other sensitive data.
6. If the staged content differs materially from the confirmed description, stop and ask again.

Only then generate the final Commit Message. Do not execute `git add`, `git commit`, `git push`, `git merge`, or `git rebase`.

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

Do not provide a branch name, PR title, explanation, code block, or Git command in the final output unless the user explicitly asks for it. After the five questions have been answered and the user confirms generation, output only one line in this format:

```text
<type>(<scope>): <中文描述>
```

This Skill never executes `git push`, `git merge`, or `git rebase`. The user is responsible for applying the generated Commit Message and performing any later Git operation.

## Standard response flow

For a Git task, follow this order:

1. Inspect the repository status, current branch, and relevant diff.
2. Identify unrelated or pre-existing changes and protect them.
3. Recommend the branch name, intended file scope, and PR title when relevant.
4. Do not stage files or change Git state.
5. Before generating output, ask the five required questions.
6. After explicit confirmation, re-check branch, changed diff, tests, and sensitive information.
7. Output only the final Commit Message if all checks pass.
