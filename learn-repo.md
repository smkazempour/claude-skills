# Learn a GitHub Repository

Analyze a GitHub repository to understand its structure, conventions, and coding style so you can write code that fits naturally into the project.

## Instructions

You are given a GitHub repository to study. Your goal is to thoroughly read and understand the repo so you can write code in its style.

**Repository:** $ARGUMENTS

### Step 1: Clone or fetch the repo metadata

Use the `gh` CLI to inspect the repository. Do NOT clone it locally. Instead, use `gh api` and `gh repo view` to read files directly from GitHub.

```
gh repo view <repo> --json name,description,defaultBranchRef
```

### Step 2: Read the README

Fetch and read the full README file:

```
gh api repos/{owner}/{repo}/readme --jq '.content' | base64 -d
```

If no README exists, note that and continue.

### Step 3: Explore the repository structure

Get the top-level file/directory listing:

```
gh api repos/{owner}/{repo}/git/trees/{default_branch} --jq '.tree[] | .path + " (" + .type + ")"'
```

Then recursively explore key directories (src/, lib/, app/, etc.) to understand the layout.

### Step 4: Read key files

Read the following files if they exist (use `gh api repos/{owner}/{repo}/contents/{path}` and decode the base64 content):

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
- If you encounter rate limiting from the GitHub API, pause and let the user know.
- If the $ARGUMENTS is not a valid GitHub repo reference (owner/repo or full URL), ask the user to provide one.
- Extract the `owner/repo` from full URLs like `https://github.com/owner/repo`.
