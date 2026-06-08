# Prompt 00 — Brandy Repository Inventory and Status Update

## Goal

Inventory the repositories under Brandy's active git workspace and create a clear, useful status report for each repository.

The purpose is to answer:

1. What did I pull from GitHub?
2. What is each repository for?
3. Which repositories are active, stale, duplicated, experimental, or archival?
4. Which repositories are safe to work on next?
5. Which repositories should be consolidated, archived, renamed, or ignored?
6. Which repositories need a human decision before more work is done?

This is a repository archaeology and planning pass.

Do not rewrite projects.
Do not delete repositories.
Do not push changes to remote repositories unless explicitly instructed later.
Do not run destructive cleanup.
Do not install packages inside project repos.
Do not mutate Google Drive or external services.

## Operating context

You are working on Brandy.

Primary workspace:

```text
/mnt/brandy_nvme/jevert/git

Prompt/report control repo:

/mnt/brandy_nvme/jevert/git/brandy_prompts_and_reports

Write central reports to:

/mnt/brandy_nvme/jevert/git/brandy_prompts_and_reports/reports

Write reusable scripts to:

/mnt/brandy_nvme/jevert/git/brandy_prompts_and_reports/scripts

Write run receipts/artifacts to:

/mnt/brandy_nvme/jevert/git/brandy_prompts_and_reports/runs

Available local tools may include:

git
python3
codex
opencode
ollama
standard Linux shell tools

Brandy has local Ollama available at:

http://127.0.0.1:11434

Do not expose Ollama over the network.

Repo list source

Discover repositories by scanning:

find /mnt/brandy_nvme/jevert/git -mindepth 1 -maxdepth 2 -type d -name .git -print

Ignore the internal .git directory path itself and treat the parent directory as the repository root.

Include at least these visible repositories if present:

azq
azq_ask
azq_ChatGPT_CLI_Helper
azq_cli_helper
azq_dev_Presidents
azq_guild
azq_lite
azq_practice
brandy_prompts_and_reports
carbon_graphite
gob
Graphite
indago
jubilant-parakeet
lantern
llm-council
LLM_Reseach_Documentation
lost-yam
lyra
maise_grader
Medulla
mesh-forge
pbwj
piborg
redesigned-broccoli
redesigned-octo-potato
robert
Scintilla
slurp_me_please
SOCCER
sturdy-barnacle
sturdy-fishstick
super-duper-lamp
Thought_Garden
utility-graph-standard-library
wilbur
workbench
Inventory prompt copy policy

For each repository, create a small inventory prompt copy if safe:

<repo>/prompts/00_repository_inventory_status_update.md

Rules:

If the repository already has a prompts/ directory, write the prompt copy there.
If it does not have a prompts/ directory, create it only if the repo is not read-only and not obviously a vendor/archive mirror.
Do not overwrite an existing inventory prompt unless the content is clearly older and you record the decision.
If a repository should not receive a prompt copy, record why in the central report.
The prompt copy should point back to the central control repo at `/mnt/brandy_nvme/jevert/git/brandy_prompts_and_reports` and explain that reports should be written under the central reports directory unless the repo already has its own report convention.
OpenCode worker plan

Use OpenCode for read-only or low-risk repository inspection when available.

Preferred OpenCode behavior:

Use opencode run
Use --agent plan for read-only analysis
Use the local Ollama model first:
ollama/qwen2.5-coder:7b
Attach the repository path using --dir
Tell OpenCode not to modify files unless explicitly asked
Capture stdout/stderr per repository

Example pattern:

opencode run \
  --model ollama/qwen2.5-coder:7b \
  --agent plan \
  --dir "<repo_path>" \
  "Inventory this repository. Do not edit files. Summarize purpose, languages, entry points, tests, docs, git status, risks, and next recommended action."

If OpenCode is unavailable, fails, hangs, or produces weak output, fall back to deterministic shell/Python inspection.

Codex is responsible for supervising the run, not blindly trusting OpenCode.

Required per-repository inspection

For each repository, collect:

Path
Repo name
Git remote URLs
Current branch
Recent commits
Git status
Primary languages and file counts
README or top-level documentation summary
Main scripts/packages/config files
Test framework or validation commands if discoverable
Presence of prompt/report conventions:
prompts/
reports/
docs/
AGENTS.md
CLAUDE.md
.opencode/
Makefile
pyproject.toml
package.json
Whether the repo appears:
active
stale
experimental
duplicated
archival
unknown
Whether it seems related to:
Thought Garden
mesh-forge / Argument Engine
SOCCER / cluster / GPU work
teaching/coursework
AZQ family
personal utilities
old experiments
What the next useful action should be.
Required central outputs

Create or update:

reports/00_repository_inventory_status_update_report.md
reports/repository_index.md
reports/repository_status_table.csv
reports/repository_status.json
runs/<timestamp>/repo_inventory_raw/
runs/<timestamp>/opencode_logs/
runs/<timestamp>/shell_inventory/

The final report must include:

Summary
Commands run
Tool versions:
codex version
opencode version
ollama version
python version
git version
Repository count
Repository table
Grouping by likely family/project
Best repos to work on next
Repos that need human decisions
Repos that look abandoned or duplicate
Repos with uncommitted work
Repos missing remotes
Repos missing README or docs
Repos that already have prompt/report discipline
Repos that received a copy of this inventory prompt
Repos skipped for prompt-copy and why
OpenCode successes/failures
Limitations
Next recommended prompt
Report table columns

Use these columns in Markdown and CSV:

repo_name
path
remote
branch
dirty_status
last_commit_date
primary_language_guess
family
classification
has_readme
has_prompts
has_reports
has_agents
has_tests
opencode_status
prompt_copied
next_action
notes
Safety rules

Do not delete repositories.
Do not push.
Do not run package installs inside repositories.
Do not run long build/test suites unless the repo clearly documents a fast check.
Do not modify source files except creating the inventory prompt copy and central reports/artifacts.
Do not change remotes.
Do not rewrite history.
Do not expose secrets.
Do not include private keys, tokens, .env secrets, or credential files in reports.
Do not crawl outside /mnt/brandy_nvme/jevert/git.

Validation

Run:

git -C /mnt/brandy_nvme/jevert/git/brandy_prompts_and_reports status --short
python3 --version
git --version
opencode --version || true
ollama --version || true

If scripts are created, run Python syntax checks:

python3 -m py_compile scripts/*.py

If generated JSON is created, validate it:

python3 -m json.tool reports/repository_status.json >/dev/null
Commit policy

Do not commit unless explicitly instructed.

Leave the tree reviewable.

At the end, report:

Whether OpenCode was installed and usable
Whether local Ollama was used
Model used
Number of repositories found
Number of repositories inventoried
Number of prompt copies created
Number of repositories skipped
Reports written
Any failures/blockers
Current git status --short for brandy_prompts_and_reports
Recommended next prompt
