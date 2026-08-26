---
name: "skill-updater"
description: "Update installed Codex Skills from the current local repository when explicitly requested."
metadata:
  short-description: "Update local Codex Skills safely"
---

# Skill Updater

Use this Skill only when the user explicitly asks to update or sync a Codex Skill installed on the local machine.

## Response language

Always answer the user in Chinese. All progress, results, errors, and final responses from this Skill must be in Chinese.

## Workflow

1. Identify the source Skill. If the user provides a GitHub Skill URL, use that URL as the source and update only that Skill. Otherwise inspect the current repository for Skill directories containing `SKILL.md`. If several local Skills are changed and the target is ambiguous, list the candidates and ask which one to update.
2. Identify the local Codex Skills directory: use `%CODEX_HOME%\skills` when `CODEX_HOME` is set; otherwise use the current user's `.codex\skills` directory. Do not assume the repository directory is the installed directory.
3. Check that the source Skill has a valid `SKILL.md` and that its front matter `name` matches the Skill directory name. Do not update a different Skill because of a similar name.
4. Before changing the installed Skill, move its existing directory to a sibling backup directory with a timestamp. Keep the exact backup path for possible restoration during this update.
5. Copy the complete source Skill directory into the original installed path. Replacing the complete directory prevents files removed from the source from remaining in the installed copy.
6. Verify that the installed copy contains `SKILL.md` and that its front matter still matches the directory name. If validation or copying fails, restore the backup and report the failure.
7. After copying and validation succeed, recursively delete the exact backup directory created in step 4, including all files inside it. Do not delete any other directory, and do not clean up the backup before validation succeeds.
8. Report the updated Skill, source path, and installed path. Confirm that the temporary backup directory and its contents were removed. Tell the user to start a new Codex turn/task for the updated Skill to take effect.

## Boundaries

- Use the current local repository as the source of truth unless the user provides or explicitly requests a remote/GitHub source URL.
- Do not require the user to push local changes before updating.
- Do not create scripts, junctions, symlinks, or automations for this task.
- Do not commit, push, pull, or discard repository changes unless the user explicitly asks.
- Do not update or remove unrelated Skills.
- If the installed path is a junction or symlink, stop before replacing it and explain that it is linked to another location.
