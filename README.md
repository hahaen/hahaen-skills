# hahaen-skills

这是一个用于 Codex 的 Skill 集合仓库，后续将持续添加不同用途的 Skill。

当前包含：

- `git-development-standard`：统一 Git 分支、提交和协作流程规范。
- `skill-updater`：将当前仓库中更新后的 Skill 安全同步到本机 Codex。

新增、删除或改名 Skill 后，请同步更新本列表及相关文档。

## 安装示例

在 Codex 中使用以下命令安装 Skill（示例）：

```text
$skill-installer
install https://github.com/hahaen/hahaen-skills/tree/main/skill-updater
```

指定目录中包含 `SKILL.md`，安装后即可使用对应 Skill。安装 `skill-updater` 后，显式调用 `$skill-updater` 即可更新当前仓库中的 Skill。Skill 改名时，请同步更新本示例中的名称和路径。

## 更新示例

使用已安装的 `skill-updater` 更新指定 Skill：

```text
$skill-updater
更新 https://github.com/hahaen/hahaen-skills/tree/main/git-development-standard
```
