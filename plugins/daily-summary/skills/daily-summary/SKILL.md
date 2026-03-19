---
name: daily-summary
description: Generate a daily status update formatted for Slack. Use whenever the user asks for a daily summary, standup update, status report, EOD update, or wants to share what they worked on today. Also trigger when the user says "what did I do today", "daily update", "standup", "share progress", "status update for slack", or "end of day". Even if the user just says "daily" or "summary" in a work context, this skill likely applies.
---

# Daily Summary for Slack

Generate a Slack-formatted daily status update by gathering data from multiple sources, cross-referencing them, and producing a concise update ready to paste into Slack.

## Strategy

The goal is to produce an accurate picture of the user's day. Different data sources overlap — a Jira ticket may have a corresponding PR, a git commit may relate to a planning phase. Cross-reference everything to avoid duplicates and to enrich each item with full context (ticket + PR + description).

## Step 1: Gather Data

Collect from ALL sources in parallel where possible. Cast a wide net — it's better to gather too much and filter than to miss work.

### Jira (primary work tracker)

```bash
# Recently updated issues assigned to user
jira issue list --jql 'project = MGMT AND labels = OSAC AND assignee = currentUser() AND updated >= -2d' --plain

# Then view each non-Epic issue for status details
jira issue view <KEY> --plain
```

### GitHub PRs (across all OSAC repos)

```bash
# Check each repo for recent PRs
for repo in fulfillment-service osac-aap osac-templates osac-operator osac-installer; do
  gh pr list --author @me --state all \
    --search "updated:>=$(date -d '2 days ago' +%Y-%m-%d)" \
    --repo osac-project/$repo 2>/dev/null
done
```

### Git history (local commits, including submodules)

```bash
# Root repo commits
git log --oneline --since="2 days ago" --author="$(git config user.name)"

# Submodule commits
git submodule foreach --quiet \
  'echo "=== $name ===" && git log --oneline --since="2 days ago" --author="$(git config user.name)" 2>/dev/null'
```

### Planning context

Read these files if they exist — they provide context about what phase/milestone is active:
- `.planning/STATE.md`
- `.planning/ROADMAP.md`
- `.planning/config.json` (contains Jira ticket mappings for phases)

### Session context

Include any work discussed or completed in the current conversation session.

### Memory files

Check auto-memory for recent project context:
- `~/.claude/projects/-home-eran-go-src-github-eranco74-osac-project/memory/MEMORY.md`

## Step 2: Cross-Reference and Deduplicate

- Match PRs to Jira tickets by looking for ticket keys (e.g., `MGMT-12345`) in PR titles, branch names, and commit messages
- Group related items together under their parent Epic
- If a git commit relates to a PR that relates to a Jira ticket, present it as one unified item, not three

## Step 3: Categorize

Classify each work item:

| Category | Criteria |
|----------|----------|
| **Accomplished** | Jira status is Done/Closed, PRs are merged, tasks fully completed |
| **Key Effort** | Actively in progress, partial completion, open PRs |
| **Risks/Challenges** | Blockers, failing tests, dependency issues, anything stalled |

## Step 4: Format for Slack

Slack uses its own "mrkdwn" format — NOT standard Markdown. Key differences:

- **Links**: Use Slack's native link syntax `<URL|display text>` to make text clickable. Do NOT use markdown `[text](url)` — Slack won't render it.
- **Jira IDs as links**: Every Jira ticket key (e.g., `MGMT-1234`) must be a clickable link using `<https://issues.redhat.com/browse/MGMT-1234|MGMT-1234>`. This applies everywhere a Jira ID appears — epics, tickets, mentions in descriptions.
- **PR links**: Use `<https://github.com/osac-project/repo/pull/123|repo#123>` for inline PR references, or put the full URL on its own line for Slack to auto-unfurl.

Use these Slack emoji codes:
- `:rocket:` — section header for accomplishments
- `:checked:` — completed item
- `:merged2:` — PR merged
- `:review:` — PR awaiting review
- `:in-progress:` — work in progress
- `:typingcat:` — actively coding
- `:person_climbing:` — section header for key effort
- `:rotating_light:` — section header for risks

### Output Template

```
:rocket: Accomplishments

<https://issues.redhat.com/browse/EPIC-KEY|EPIC-KEY>
:checked: <https://issues.redhat.com/browse/TICKET-KEY|TICKET-KEY>: <Summary>
<1-2 line description of what was done>
:merged2: All PRs merged
<PR URL on its own line>

:person_climbing: Key Effort

<https://issues.redhat.com/browse/EPIC-KEY|EPIC-KEY>
:in-progress: <https://issues.redhat.com/browse/TICKET-KEY|TICKET-KEY>: <Summary>
<Brief status — what's done, what remains>
:review: PR ready for review / :typingcat: Still coding
<PR URL on its own line>

:rotating_light: Risks & Challenges

<Any blockers or concerns, or "None — smooth sailing">
```

## Rules

- Group items under their parent Epic key
- Make every Jira ID clickable using Slack link syntax: `<https://issues.redhat.com/browse/KEY|KEY>`
- Include full GitHub PR URLs: `https://github.com/osac-project/<repo>/pull/<number>`
- Never use markdown link syntax `[text](url)` — Slack won't render it. Use `<URL|text>` instead.
- Keep descriptions concise: 1-2 lines per item
- If work isn't tracked in Jira, include it under the most relevant epic
- If no work is found from automated sources, ask the user what they worked on before producing output
- Present the draft to the user for review before they paste it — they may want to add or remove items
