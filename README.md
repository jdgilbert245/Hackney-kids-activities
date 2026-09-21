# Kids' week in Hackney

A weekly calendar of children's activities in Hackney, so parents don't each have to keep track of everything themselves.

## How it works

The site is a single page (`index.html`) hosted on GitHub Pages. Everything it shows comes from one file, `events.json`.

Every Monday at 05:00 UTC, a GitHub Action (`.github/workflows/weekly-update.yml`) runs a Claude agent. The agent:

1. reads the instructions in `AGENT.md`
2. checks the sources listed there (children's centres, libraries, leisure centres, museums, cinemas) and runs a few web searches
3. adds new events to `events.json`, corrects changed ones and removes expired ones
4. opens a pull request listing what it added, changed, removed and couldn't confirm

Nothing goes live until the pull request is reviewed and merged. Merging updates the site within a minute or two.

## Making changes

### Add, fix or remove an event by hand

Edit `events.json` on GitHub (open the file, click the pencil icon). Each event is one entry; the format is described in `AGENT.md` under "Entry format". Commit the change and the site updates. The agent updates existing entries rather than starting from scratch, but check its next pull request doesn't undo your edit.

### Add something the agent can't confirm

Some details aren't published online (an age range missing from a timetable, a start time you only know from the venue). Add them to `checked-by-hand.md`, one line each, with a recheck date:

```
- Comet Children's Centre, Baby Club: ages 0 to 1. Checked 2026-09-22. Recheck by 2026-12-19.
```

The agent uses the fact until the recheck date, as long as the session is still listed at its source. It flags the fact in its pull request two weeks before the date and stops using it after.

### Add an event the agent can't read

For a source the agent can't read, such as a timetable that only loads in a browser, add the event to `events.json` yourself with `"manual": true` and a `"checkBy"` date. The agent won't touch it, and flags it when the date is near. The website hides it once the date has passed, so an out-of-date entry can't linger.

### Change what the agent looks for

Edit `AGENT.md`. It's written in plain English: add or remove sources under "Sources", or change the rules, such as which ages count or how far ahead to look. Changes apply from the next run.

### Run the agent now instead of waiting for Monday

Go to Actions, open "Weekly events update", and click "Run workflow".

### Change when it runs

Edit the `cron` line in `.github/workflows/weekly-update.yml`. `"0 5 * * 1"` means 05:00 UTC every Monday.

### Change the website itself

The layout, filters and planner are all in `index.html`. Each visitor's filter and nap settings are saved in their own browser and never sent anywhere.

## Reviewing the weekly pull request

- Skim the Added and Changed lists and open a few of the links.
- Check Couldn't confirm for anything worth adding by hand.
- Check Sources that failed. If a source fails every week, fix or remove it in `AGENT.md`.
- Check Hand checks due, and update the dates in `checked-by-hand.md` or `events.json` for anything you've rechecked.
- Merge, or close the pull request to discard that week's changes.

## Setup notes

- The repo must be public for free GitHub Pages hosting.
- The Claude GitHub App must be installed on the repo.
- A secret named `ANTHROPIC_API_KEY` (or `CLAUDE_CODE_OAUTH_TOKEN` for a Claude subscription) must be set under Settings → Secrets and variables → Actions.
- Settings → Actions → General must allow GitHub Actions to create pull requests.
