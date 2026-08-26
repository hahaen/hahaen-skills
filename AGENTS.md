# 项目级 Agent 规则

## 项目定位

`hahaen-skills` 是用于 Codex 的 Skill 集合仓库。仓库中的每个顶层目录代表一个独立 Skill。

## Skill 目录与命名

- 新增 Skill 时，使用一个独立的顶层目录，并在其中放置该 Skill 的 `SKILL.md`。
- Skill 目录名使用小写 kebab-case，例如 `git-development-standard`。
- `SKILL.md` 的 front matter 中的 `name` 必须与目录名一致。
- 如果 Skill 使用 Codex 的展示配置，应同步维护该 Skill 下的 `agents/openai.yaml`，其中的展示名称和提示词也必须使用最新名称。
- 每个 Skill 都必须明确要求 AI 最终使用中文回答用户；新增或修改 Skill 时，必须在 `SKILL.md` 和适用的 `agents/openai.yaml` 提示词中同步保留这一要求。
- Skill 改名时，目录名、`SKILL.md` 的 `name`、`agents/openai.yaml` 中的相关名称、README 中的名称和安装示例必须一起更新，不能只改其中一处。

## README 维护规则

- README 的“当前包含”部分必须列出仓库中当前存在的每个 Skill，并使用目录名作为 Skill 名称。
- 新增、删除或改名 Skill 后，必须在同一次修改中同步更新 README 的列表和相关描述。
- README 的“安装”部分只保留一个完整的安装示例，用于说明安装命令格式；不要为每个 Skill 分别添加安装命令。
- 安装示例必须指向当前仍存在的 Skill。示例所指向的 Skill 被删除或改名时，必须同步更换或修正示例。
- README 中的名称、目录路径、链接和命令必须与实际文件结构保持一致。

## 修改流程

处理仓库变更时，按以下顺序检查：

1. 先查看现有目录、README 和相关 Skill 文件，确认当前名称与结构。
2. 实施新增、删除或改名时，搜索旧名称的全部引用，并同步更新受影响的文档和配置。
3. 检查 README 是否完整列出当前 Skill，且安装区域仍然只有一个示例。
4. 检查 `SKILL.md` front matter、目录名和 `agents/openai.yaml` 的名称是否一致。
5. 检查每个 Skill 是否明确要求 AI 最终使用中文回答用户，并在新增或修改时同步更新相关说明和提示词。
6. 保留用户已有的无关修改，不擅自覆盖、丢弃或提交它们。

除非用户明确要求，不要为了更新文档而执行 Git 提交、推送、合并、变基或其他会改变 Git 状态的操作。
