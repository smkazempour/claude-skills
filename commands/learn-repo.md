# Learn a GitHub Repository

Analyze a repository — either a remote GitHub repo passed as an argument, or the local clone in the current working directory — to understand its structure, conventions, and coding style so you can write code that fits naturally into the project.

## Instructions

You are given a repository to study. Your goal is to thoroughly read and understand it so you can write code in its style.

**Repository:** $ARGUMENTS

### Step 0: Determine the mode

If `$ARGUMENTS` is non-empty, operate in **remote mode**: treat it as a GitHub repo reference (owner/repo or full URL). Pick a transport:
1. First check whether the `gh` CLI is available (`gh --version`). If yes, use **gh transport** and read files directly from GitHub via `gh api` and `gh repo view` — do NOT clone.
2. If `gh` is unavailable (e.g., not installed on this machine), fall back to **git transport**: shallow-clone the repo into a temporary directory and then proceed using the **Local** commands in the remaining steps. Example:
   ```
   git clone --depth=1 https://github.com/{owner}/{repo}.git <tempdir>
   ```
   Pick a tempdir outside the user's working tree (e.g., the OS temp dir). Tell the user where you cloned and offer to delete it after Step 6.
3. If neither `gh` nor `git` is available, stop and tell the user.

If `$ARGUMENTS` is empty or blank, operate in **local mode**:
1. Run `git rev-parse --show-toplevel` to confirm the current working directory is inside a git repository.
2. If it is, use that working tree as the repo to study — read files with the `Read`, `Glob`, and `Grep` tools instead of `gh api`. Optionally run `git remote -v` to identify the upstream GitHub repo for context in the final summary.
3. If it is not a git repository, stop and ask the user to either provide a repo reference or `cd` into a local clone.

The remaining steps describe both modes. In remote mode with the **git transport** fallback, use the **Local** commands after cloning. Otherwise use the command appropriate to your mode.

### Step 1: Inspect repo metadata

- **Remote:** `gh repo view <repo> --json name,description,defaultBranchRef`
- **Local:** `git remote -v`, `git log -1 --oneline`, and `git branch --show-current` to identify the upstream and current state.

### Step 2: Read the README

- **Remote:** `gh api repos/{owner}/{repo}/readme --jq '.content' | base64 -d`
- **Local:** `Read` the `README.md` (or `README.rst`, `README`) at the repo root.

If no README exists, note that and continue.

### Step 3: Explore the repository structure

Get the top-level file/directory listing:

- **Remote:** `gh api repos/{owner}/{repo}/git/trees/{default_branch} --jq '.tree[] | .path + " (" + .type + ")"'`
- **Local:** Use `Glob` (e.g., top-level `*` then targeted patterns like `src/**`, `tests/**`) to map the layout.

Then recursively explore key directories (src/, lib/, app/, etc.) to understand the layout.

### Step 4: Read key files

Read the following files if they exist. In remote mode, use `gh api repos/{owner}/{repo}/contents/{path}` and decode the base64 content. In local mode, use the `Read` tool directly.

- **Configuration files**: `package.json`, `pyproject.toml`, `setup.cfg`, `setup.py`, `Cargo.toml`, `go.mod`, `Makefile`, `CMakeLists.txt`, or equivalent
- **Linting/formatting configs**: `.eslintrc*`, `.prettierrc*`, `ruff.toml`, `.flake8`, `.editorconfig`, `rustfmt.toml`, etc.
- **CI/CD**: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `Jenkinsfile`
- **Project instructions**: `CLAUDE.md`, `CONTRIBUTING.md`, `ARCHITECTURE.md`, `DEVELOPMENT.md`
- **Type definitions / interfaces**: Main type files, schema files, or model definitions

### Step 5: Sample representative source files

Read 3–5 representative source files from the main codebase to understand:

- Naming conventions (camelCase, snake_case, PascalCase, etc.)
- Import organization and ordering
- Comment style and documentation patterns
- Error handling approach
- Testing patterns (read 1–2 test files if a test directory exists)
- Module/package organization

Pick files that are neither trivially small nor excessively large — aim for files that demonstrate the core logic of the project.

### Step 6: Produce a summary

After reading everything, produce a structured summary with these sections:

1. **Project Overview** — What the project does, its main purpose, and key technologies
2. **Repository Structure** — Directory layout and what lives where
3. **Language & Framework** — Primary language, framework, major dependencies
4. **Coding Conventions** — Naming, formatting, import style, comment style, and any enforced rules
5. **Architecture Patterns** — How code is organized (MVC, modules, microservices, etc.), key abstractions, data flow
6. **Testing Approach** — Test framework, naming conventions, what gets tested
7. **Build & Development** — How to build, run, and test the project
8. **Style Guide for New Code** — Concrete, actionable rules for writing code that matches this repo's style. Include DO and DON'T examples drawn from actual code in the repo.

### Important Notes

- If the repo is very large, focus on the most important directories and files rather than trying to read everything.
- If you encounter rate limiting from the GitHub API (remote mode with gh transport), pause and let the user know. Consider falling back to the git-clone transport if it would unblock progress.
- If you used the git-clone fallback, after completing the summary in Step 6, ask the user whether to delete the temp clone. Do not delete it without asking.
- If `$ARGUMENTS` is non-empty but not a valid GitHub repo reference (owner/repo or full URL), ask the user to provide one.
- If `$ARGUMENTS` is empty AND the current working directory is not inside a git repository, ask the user to either provide a repo reference or `cd` into a local clone.
- In remote mode, extract `owner/repo` from full URLs like `https://github.com/owner/repo`.
- In local mode, exclude untracked or build-artifact directories (e.g., `node_modules/`, `.venv/`, `dist/`, `build/`, `.git/`) when sampling files.
