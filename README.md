# Kids' week in Hackney

A weekly calendar of children's activities in Hackney, so that no one parent has to keep track of everything.

## How it works

The site is a single page (`index.html`) hosted on GitHub Pages. Everything it shows comes from one file, `events.json`.

Every Monday at 05:00 UTC, a GitHub Action (`.github/workflows/weekly-update.yml`) runs a Claude agent. The agent:

1. reads the instructions in `AGENT.md`
2. checks the listed sources (children's centres, libraries, leisure centres, museums, cinemas and so on) plus a general web search
3. updates `events.json`: adds new events, corrects changed ones, removes expired ones
4. opens a pull request summarising what it added, changed, removed and couldn't confirm

Nothing goes live until the pull request is reviewed and merged. Merging updates the site within a minute or two.

## Making changes

**Add, fix or remove an event by hand**
Edit `events.json` on GitHub (open the file, click the pencil icon). Each event is one entry; the format is described in `AGENT.md` under "Entry format". Commit the change and the site updates. The agent will keep your edits in future runs.

**Change what the agent looks for**
Edit `AGENT.md`. It's plain English: add or remove sources under "Sources", or adjust the rules (for example which ages count, or how far ahead to look). Changes take effect on the next run.

**Run the agent now instead of waiting for Monday**
Go to Actions, open "Weekly events update", and click "Run workflow".

**Change when it runs**
Edit the `cron` line in `.github/workflows/weekly-update.yml`. `"0 5 * * 1"` means 05:00 UTC every Monday.

**Change the website itself**
Everything (layout, filters, planner) is in `index.html`. Filter settings are saved in each visitor's own browser; no personal data is collected.

## Reviewing the weekly pull request

- Skim the **Added** and **Changed** lists; spot-check a few links.
- Check **Couldn't confirm** for anything worth adding by hand.
- Check **Sources that failed**: if a source fails every week, fix or remove it in `AGENT.md`.
- Merge, or close the pull request to discard that week's changes.

## Setup notes

- The repo must be public for free GitHub Pages hosting.
- The Claude GitHub App must be installed on the repo.
- A secret named `ANTHROPIC_API_KEY` (or `CLAUDE_CODE_OAUTH_TOKEN` for a Claude subscription) must be set under Settings, Secrets and variables, Actions.
- Settings, Actions, General must allow GitHub Actions to create pull requests.
