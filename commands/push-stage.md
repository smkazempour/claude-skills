# Push a Stage of Work to Remote

Commit the current changes as a self-contained **stage** (a milestone block of work): create a short-lived branch, commit there, merge it back into your working branch with `--no-ff` so the stage shows up as one grouped unit in history, push, then delete the stage branch. The end state is a single clean local branch plus a permanent, revertable record of the stage.

Use `/push` for routine linear commits. Use this when you want a stage to be a distinct, self-contained block in the history graph (with a merge commit you can later revert or inspect as a whole).

**Arguments:** $ARGUMENTS

## Argument Parsing

Parse `$ARGUMENTS` for the following flags and values. All are optional:

- `--name <stage-name>` — the name for the stage branch and merge commit. Must be a valid git branch name (lowercase, hyphen-separated, no spaces). If omitted, derive a short kebab-case slug from the commit message; if no message is available either, fall back to `stage-<n>` with the smallest `<n>` that is unused.
- `--update-docs` — Before committing, review the changes and update `README.md` and `CLAUDE.md` to reflect the current state of the project (same behavior as `/push`).
- `--file <path>` — Only stage the specified file(s). May be repeated. If omitted, stage all changes.
- Any remaining text is the **commit message**. If none is given, generate one from the staged changes.

### Usage Examples

```
/push-stage --name complete-grid-pipeline Replace firm-merge with grid pipeline
/push-stage Refactor yield models             # stage name auto-derived from the message
/push-stage --name docs-pass --update-docs Update README for the new pipeline
/push-stage --name hotfix --file src/app.py Patch the startup crash
```

## Instructions

### Step 1: Assess the current state

- `git rev-parse --abbrev-ref HEAD` — capture this as **BASE**: the branch you will merge back into and push.
- `git status`, `git diff`, `git diff --cached` — see what will be committed.
- `git log --oneline -5` — match the existing commit style.
- `git remote -v` and `git branch -vv` — confirm the remote and BASE's tracking branch.

If the working tree is clean (nothing staged, unstaged, or untracked to include), tell the user and stop.

### Step 2: Choose the stage name

- If `--name` was given, use it. Validate it is a legal branch name and does not already exist (`git rev-parse --verify <name>` should fail). If it exists, append `-2`, `-3`, … until unique, or ask the user.
- If `--name` was not given, slugify the commit message into a short kebab-case name (e.g. "Refactor yield models" → `refactor-yield-models`). If there is no message, use `stage-<n>` with the smallest unused `<n>`.

### Step 3: Update documentation (only if `--update-docs` is set)

Follow the same procedure as `/push`: analyze the changes, update an existing `README.md` (create one only if it is missing), and update `CLAUDE.md` only if it already exists, preserving the existing style and tone. Show the user a summary of the doc edits and confirm before proceeding. The doc changes are committed as part of the stage.

### Step 4: Create the stage branch and commit

1. `git switch -c <stage-name>` — this branches from BASE and carries the working-tree changes onto the stage branch.
2. Stage the changes: `git add <path>` for each `--file`, or `git add -A` otherwise. Run `git status` to confirm exactly what will be committed.
3. Decide the commit message: use the user's text if provided, otherwise generate a concise message (1–2 sentences, focused on the "why", matching the `git log` style).
4. Show the proposed commit message and ask for confirmation, then create the commit. Follow the repository's commit conventions (including any required trailers).

### Step 5: Merge the stage back into BASE

1. `git switch <BASE>`.
2. `git merge --no-ff <stage-name> -m "Merge branch '<stage-name>'"` (you may add a one-line summary body). `--no-ff` is required: it forces a merge commit so the stage stays grouped in history even though it could fast-forward.
3. If the merge reports conflicts, do **not** resolve them automatically. Run `git merge --abort`, restore the user to a clean state, and explain. (Conflicts are unlikely because BASE has not moved since the branch was created, but possible if BASE advanced.)

### Step 6: Push BASE

- If BASE has no upstream, push with `git push -u origin <BASE>`; otherwise `git push`.
- Only BASE is pushed — never push the stage branch.
- Report success and the remote URL. If the push is rejected, do not force-push: tell the user and suggest `git pull --rebase` and then retry.

### Step 7: Delete the stage branch

- `git branch -d <stage-name>` (lowercase `-d`, which deletes only if the branch is fully merged — a built-in safety check).
- Confirm to the user that the branch was deleted, and show `git log --graph --oneline -6` so they can see the merge commit and the preserved branch topology.

### Important Notes

- History is preserved by the `--no-ff` merge commit, not by the branch name — deleting the branch afterward loses nothing.
- Never force-push. Never push secrets, credentials, `.env` files, or data files; respect `.gitignore` and warn the user if any data files or large binaries are staged.
- Do not push the stage branch to the remote; only the integration branch (BASE) is pushed.
- Always show the user what will be committed, and the merge, before acting.
- If any step fails (commit, merge, or push), stop and report the exact state rather than improvising a recovery.
