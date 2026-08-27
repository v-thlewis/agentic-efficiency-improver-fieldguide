# GitHub Agentic Efficiency Improver: Quickstart Guide

Set up automated energy efficiency improvements for your codebase in under 10 minutes using GitHub Agentic Workflows.

---

## Prerequisites

Before you start, verify you have:

- **GitHub CLI v2.0.0+** — Check with `gh --version`
- **GitHub account** with write access to a repository
- **GitHub Actions enabled** in your repository
- **AI provider account** — Choose one:
  - GitHub Copilot (recommended)
  - Claude (Anthropic)
  - OpenAI
  - Google Gemini
- **API key or personal access token** ready for your chosen AI provider
- **Test repository** with some commit history (for meaningful optimization results)

---

## Setup & Run in 5 Steps

### 1. Install the GitHub Agentic Workflows Extension

```bash
gh extension install github/gh-aw
```

Verify installation:
```bash
gh aw --help
```

### 2. Navigate to Your Repository & Launch the Wizard

```bash
cd your-repository
gh aw add-wizard githubnext/agentics/efficiency-improver
```

The wizard will guide you through configuration. No manual file editing needed.

### 3. Select Your AI Provider & Add Credentials

When prompted, choose your AI provider:
- **GitHub Copilot** (integrates natively with GitHub)
- **Claude** (Anthropic)
- **OpenAI**
- **Google Gemini**

Then provide your API credentials. The wizard automatically stores them as repository secrets—they're never exposed in logs or workflow files.

### 4. Review & Merge the Pull Request

The wizard creates a pull request with:
- `.github/workflows/efficiency-improver.lock.yml` — The compiled GitHub Actions workflow
- `efficiency-improver.md` — The markdown source file

Review the PR, then **merge it**. The workflow will automatically start running.

### 5. Monitor & Review Results

1. Go to the **Actions** tab in your repository
2. Watch the `Efficiency Improver` workflow run (takes up to 60 minutes)
3. Once complete, check the **Pull Requests** tab for optimization suggestions
4. Review generated PRs with before/after metrics on energy efficiency

---

## What Gets Generated

- **Pull Requests** with concrete code optimizations and energy savings metrics
- **Comments on Issues** with AI-driven efficiency suggestions
- **Activity Summaries** tracking optimization progress and energy savings
- **Transparent Attribution** clearly disclosing AI involvement and methodology

---

## Auditing Results: Has It Actually Improved Things?

### Should you do this?

After the Efficiency Improver has been running for a while and has opened several PRs, it's worth periodically **auditing its track record**. Do this if:

- The workflow has created more than a handful of PRs and you want a single at-a-glance view of which ones were real wins.
- You want to merge its PRs with confidence and need to know which claims are backed by solid measurements versus synthetic benchmarks or estimates.
- You suspect regressions may have slipped through — a PR whose own numbers show the code got *slower*, but which was merged anyway.
- You're reporting on the value the workflow delivers and need an honest, evidence-based scorecard rather than the agent's own prose claims.

### Why it matters

The Efficiency Improver is measurement-driven, but not every claimed gain holds up under scrutiny. Benchmarks sometimes exercise a simplified stand-in instead of the real code path, results can be mixed (some cases improve, others regress), and near-duplicate PRs can redo the same work because the underlying code was reset rather than organically regressing. There is also no cumulative tracking file in the repo by default, so regressions and unverified claims are easy to miss without a deliberate review. Auditing closes that gap and catches the exact failure mode this workflow is meant to avoid: shipping a change that makes things worse.

### How to run the audit

Paste the prompt below into an AI coding agent (e.g., GitHub Copilot) with access to this repository and the GitHub CLI. It produces a red/yellow/green scorecard of every efficiency PR, judged against each PR's *own* measurement data.

````markdown
Audit this repository's Efficiency Improver agentic workflow results and produce a
scorecard table of every efficiency PR it has created, with a red/yellow/green
light showing whether each PR's own measurement data actually supports its claim.

## Step 1: Confirm the workflow exists

Look for the Efficiency Improver workflow definition, typically at
`.github/workflows/efficiency-improver.md` (source workflow) and/or
`.github/workflows/efficiency-improver.lock.yml` (compiled GitHub Actions workflow).
Also check `.github/workflows/*.md` more broadly in case it's named differently —
look for a workflow whose frontmatter or body identifies it as "Efficiency Improver"
or similar (an AI agent focused on energy/performance efficiency).

If no such workflow file exists anywhere in the repo:
STOP and report: "No Efficiency Improver workflow is implemented in this repository.
No data to analyze." Do not proceed further.

## Step 2: Find its PRs

Determine the PR title prefix the workflow uses (check the `title-prefix` field under
`create-pull-request` in the workflow's frontmatter — commonly `[efficiency-improver] `).
Then run:

  gh pr list --repo <owner>/<repo> --state all --search 'in:title "<prefix>"' \
    --json number,title,state,url,mergedAt,createdAt --limit 100

If this returns zero results:
STOP and report: "The Efficiency Improver workflow is configured but has not yet
opened any pull requests. No data to analyze yet." Do not proceed further.

If it returns results, continue.

## Step 3: Pull and read every PR body

For each PR number returned, fetch its full body:

  gh pr view <number> --repo <owner>/<repo> --json body -q '.body'

Read every PR body in full. Do not skip any — duplicates and closed/superseded PRs
are informative too.

## Step 4: Extract, per PR

- A short plain-language description of what the PR changed
- The claimed/predicted efficiency gain (the headline number or statement)
- The PR's own reported "before" and "after" measurements
- Whether the benchmark actually exercised the real code path, or a simplified
  stand-in/simulation (e.g. a fake object, a sleep() call, a hardcoded literal
  instead of calling the real dependency) — read the "Reproducibility" /
  benchmark code snippet closely, not just the prose claims
- Whether the PR's own numbers show every case improved, a mix of improved and
  regressed, or a regression across the board
- Whether the PR is a near-duplicate of an earlier PR fixing the same code
  (same function/file, similar before-state) — if so, check git log for a commit
  between them that reintroduced the issue, and note this rather than treating it
  as an independent data point
- Merge state: merged / open / closed-without-merging

## Step 5: Assign a light to each PR that landed or proposes a real change

(Skip lights — use ⚪ N/A — for pure infrastructure PRs like "add benchmark.py"
itself, and for closed/superseded duplicates with no independent data.)

🟢 Green — the benchmark measured the real code path (not a stand-in), and the
   after numbers are genuinely better than before, with no material caveat.

🟡 Yellow — one of the following applies:
   - The benchmark used a synthetic stand-in / simulation instead of the real
     dependency or code path, so the claimed multiplier is unverified
   - The result was mixed: some measured cases improved, others regressed
   - The "before" measurement didn't actually run (e.g. errored out) and the
     claimed gain is an estimate/typical-case number, not a real measurement

🔴 Red — the PR's own reported numbers show every measured case got WORSE after
   the change, regardless of what the PR's prose argues, especially if it was
   still merged.

## Step 6: Output

Produce a markdown table with columns:
| PR | Merged | Change | Claimed gain | What the PR's own data actually showed |

Put the light as the first thing in the last column, e.g.
"🔴 **Regression** — every measured case was slower after the change; merged anyway"
or "🟢 **Real win** — measured on the actual code path, modest genuine gain".

After the table, include:
- A tally line: count of 🟢 / 🟡 / 🔴 among the PRs that actually landed or propose
  a change (excluding ⚪ rows)
- A short "what you need to understand about this data" section covering any
  patterns you noticed across multiple PRs — e.g. repeated use of synthetic
  benchmarks, a repeated unverified environment/runtime claim, PRs that redo work
  because the underlying code was reset/reintroduced rather than organically
  regressing, or the complete absence of any aggregated/cumulative tracking file
  in the repo (confirm this by searching for anything like `benchmarks/`,
  `history.json`, or a checked-in results file — note if none exists)

Do not soften a 🔴 finding to be diplomatic — if a merged PR's own data shows a
regression, say so plainly, since that's the exact failure mode this audit exists
to catch.
````

---

## Workflow Configuration (Optional)

The generated `efficiency-improver.md` file contains:

**Frontmatter (YAML):**
```yaml
on: 
  schedule:
    - cron: '0 9 * * *'          # Runs daily at 9 AM (edit to customize)
  workflow_dispatch: {}            # Trigger manually when needed
agents: [github-copilot]          # Change if using different AI provider
read_permissions:
  - contents: read
  - issues: read
  - pull_requests: read
safe_outputs:
  - pull_requests: write
  - issues: write
  - contents: write
```

**To customize:**
1. Edit the markdown file
2. If you change frontmatter, run: `gh aw compile` to regenerate the lock file
3. Commit and push your changes

Common customizations:
- **Change schedule:** Edit the cron expression (use [crontab.guru](https://crontab.guru/) for help)
- **Switch AI provider:** Change the `agents` field and update your API credentials in repository secrets
- **Modify permissions:** Edit `read_permissions` or `safe_outputs` (use minimal necessary permissions)

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| `gh aw` command not found | Update GitHub CLI: `gh extension upgrade gh-aw` or reinstall with `gh extension install github/gh-aw` |
| Wizard doesn't start | Run `gh auth status` to verify authentication; re-authenticate if needed with `gh auth login` |
| Workflow fails in Actions tab | Check repository secrets: Go to **Settings > Secrets and variables > Actions**. Verify API key is named correctly (e.g., `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`). Click the failed workflow and then click the section that failed and it most likely will tell you what the issue is below; also you can also ask GitHub Copilot what this issue is by clicking the 'Explain error' button. |
| Workflow takes too long | Efficiency Improver can take up to 60 minutes.|
| No PRs generated after run | Check workflow logs in Actions tab. May need multiple runs to find optimization opportunities. Repos with complex patterns yield better results |
| Want to re-run the workflow | Delete generated PRs/issues and manually trigger: `gh workflow run efficiency-improver.yml` |
| Permission denied when pushing | Verify your GitHub token has `repo` and `workflow` scopes: `gh auth status` |

---

## Need Help?

- **[GitHub Agentic Workflows Documentation](https://github.github.com/gh-aw/introduction/overview/)** — Full documentation and examples
- **[Efficiency Improver Workflow](https://github.com/githubnext/agentics/blob/main/workflows/efficiency-improver.md)** — Learn how the workflow scans your code
- **GitHub CLI Docs** — `gh aw --help` or visit [cli.github.com](https://cli.github.com/)
