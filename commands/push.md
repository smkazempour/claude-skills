# Push Changes to Remote

Stage, commit, and push local changes to the remote GitHub repository. Optionally update documentation files (README.md, CLAUDE.md) to reflect the latest state of the repo before pushing.

**Arguments:** $ARGUMENTS

## Argument Parsing

Parse `$ARGUMENTS` for the following flags and values. All are optional:

- `--update-docs` — Before committing, review recent changes and update `README.md` and `CLAUDE.md` to reflect the current state of the project.
- `--file <path>` — Only stage and push the specified file(s). Can appear multiple times (e.g., `--file src/foo.py --file src/bar.py`).
- Any text that is not a flag is treated as the **commit message**. If no commit message is provided, generate one automatically based on the changes.

### Usage Examples

```
/push                                    # push all changes, auto-generate commit message
/push fixed the login bug                # push all changes with this commit message
/push --update-docs                      # update docs, then push all changes
/push --file src/utils.py                # push only src/utils.py
/push --file src/utils.py --update-docs  # push only src/utils.py after updating docs
/push --update-docs refactored auth flow # update docs, push all changes with this message
```

## Instructions

### Step 1: Assess the current state

Run the following to understand what needs to be pushed:

- `git status` to see staged, unstaged, and untracked files
- `git diff` to see unstaged changes
- `git diff --cached` to see staged changes
- `git log --oneline -5` to see recent commit style
- `git remote -v` to confirm the remote
- `git branch -vv` to check tracking branch status

If there are no changes to commit (clean working tree with nothing staged or untracked), tell the user and stop.

### Step 2: Update documentation (if `--update-docs` is set)

If the `--update-docs` flag is present:

1. **Analyze recent changes.** Look at the diff and any new/modified/deleted files to understand what has changed in the project since the docs were last updated.

2. **Update README.md** (if it exists):
   - Read the current README.md.
   - Check whether any sections are outdated, missing, or need additions based on the changes. For example: new scripts, changed directory structure, new dependencies, updated usage instructions.
   - Edit the README.md to reflect the current state. Keep the existing style and tone. Do not rewrite sections that are still accurate.
   - If no README.md exists, create one with a basic project description, structure overview, and usage instructions.

3. **Update CLAUDE.md** (if it exists):
   - Read the current CLAUDE.md.
   - Check whether coding conventions, repository structure, file naming patterns, data processing patterns, or other instructions need updating based on the changes.
   - Edit the CLAUDE.md to reflect the current state. Preserve existing conventions that are still valid.
   - If no CLAUDE.md exists, do NOT create one — CLAUDE.md should only be updated if it already exists.

4. Show the user a summary of what was updated in the docs and ask for confirmation before proceeding.

### Step 3: Stage files

- If `--file <path>` was specified, stage only those files: `git add <path>` for each.
- If no `--file` flag, stage all changes: `git add -A`.
- After staging, run `git status` to confirm what will be committed.
- If `--update-docs` was used, the documentation changes will be included in the same commit.

### Step 4: Create the commit

- If the user provided a commit message in the arguments, use it.
- If no commit message was provided, generate one by analyzing the staged changes:
  - Summarize the nature of the changes (new feature, bug fix, refactor, docs update, etc.).
  - Write a concise message (1–2 sentences) focusing on the "why" rather than the "what".
  - Follow the commit style observed in `git log`.
- Show the proposed commit message to the user and ask for confirmation.
- Create the commit.

### Step 5: Push to remote

- Check if the current branch has an upstream tracking branch.
- If not, push with `-u` to set upstream: `git push -u origin <branch>`.
- If yes, push normally: `git push`.
- Report the result (success or failure) and show the remote URL.

### Important Notes

- Never force-push. If the push is rejected, tell the user and suggest `git pull --rebase` first.
- Never push secrets, credentials, `.env` files, or data files. Warn the user if any are staged.
- If the repo has a `.gitignore`, respect it. If data files or large binaries are staged, warn before committing.
- Always show the user what will be committed before creating the commit.
- If there are merge conflicts, do not attempt to resolve them automatically — inform the user.
