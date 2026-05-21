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
