---
name: rebbi
description: Track development work as issues in Rebbi, a project-management system, through its JSON API. Use when starting, progressing or finishing a piece of work in a project tracked in Rebbi — register an issue for it, move its status as it progresses, and record findings as comments. Triggers include "skrá mál", "create an issue", "what's on the board", "log this in Rebbi", and issue keys like REB-12 or LID-3.
---

# Rebbi — tracking work through the API

Rebbi is Hugi's project-management system. Development work on projects tracked
there is registered as issues through a small JSON API, so the board reflects what
is actually happening without anyone retyping it.

**Track as you work.** When you start a piece of work worth remembering, register an
issue and set it `InProgress`. When it is done, set `Review` — Hugi moves issues to
`Done` and `Released` himself. Record decisions and findings as comments on the
issue rather than letting them live only in a chat log, and mention issue keys
(`LID-3`) in commit messages when a commit implements one.

Not every task needs an issue. A typo fix does not; anything you would want to find
again in a month does.

## Connecting

```bash
TOKEN=$(cat ~/.config/rebbi/api-token)
BASE=https://www.godurkodi.is
```

Every request carries `Authorization: Bearer $TOKEN`. Input is form-encoded (curl
`-d` / `--data-urlencode`), output is JSON.

If the token file is missing, stop and ask Hugi for a token rather than guessing —
minting one needs database access. Never put the token in a repository, a commit
message, or an issue.

## The calls

```bash
# What projects exist (identifier, name, customer)
curl -s -H "Authorization: Bearer $TOKEN" "$BASE/api/projects"

# What is on the board
curl -s -H "Authorization: Bearer $TOKEN" "$BASE/api/issues?project=LID&status=InProgress"

# Read one issue, with its description and comments
curl -s -H "Authorization: Bearer $TOKEN" "$BASE/api/issues/LID-3"

# Register one (project + name required; text/status/emoji/assignee optional)
curl -s -H "Authorization: Bearer $TOKEN" -d "project=LID" \
  --data-urlencode "name=Short title" --data-urlencode "text=Markdown description" \
  -d "emoji=🚀" "$BASE/api/issues"

# Update it — send only what changes
curl -s -H "Authorization: Bearer $TOKEN" -d "status=InProgress" "$BASE/api/issues/LID-3"

# Comment
curl -s -H "Authorization: Bearer $TOKEN" \
  --data-urlencode "text=Root cause was …" "$BASE/api/issues/LID-3/comments"
```

Issue keys are the project identifier plus a number (`LID-3`), case-insensitive in
URLs. Lists are newest-first and capped at 200, so filter by project.

Statuses, in lifecycle order: `Memo` (registered, untriaged) → `Backlog` (waiting) →
`NotStarted` (on the board) → `InProgress` → `Review` (done, awaiting acceptance) →
`Done` (accepted) → `Released` (closed).

Errors come back as `{"error": "…"}` with 400 (bad parameter), 401 (bad token) or
404 (unknown issue or endpoint). Read the message — it names the problem.

The full reference, including every parameter and response shape, lives in
`API.md` in the Rebbi repository.

## Writing issues

- **Write in the language the project uses.** Most of Hugi's projects are Icelandic;
  match the codebase and its documentation rather than defaulting either way.
- **Name the issue as a task**, not a symptom: "Laga dagsetningar í tímaskýrslu"
  rather than "Dagsetningar bilaðar".
- **Descriptions are markdown**, and `[[Page title]]` links to the project's wiki.
  Put what a reader needs in a month: what is wrong or wanted, and any constraint
  that shapes the answer.
- **The emoji field is free-form and playful** — 🚀 for features, 🐛 for bugs, 🔥 for
  urgent, 💩 for what it sounds like. No fixed meanings.
- **Assign with a kennitala** if you assign at all — ask Hugi for the right one
  rather than guessing. Most issues need no assignee.

## What not to do

- Do not set `Done` or `Released` — those are Hugi's to give.
- Do not create a project unless asked; ask which project an issue belongs to when
  it is not obvious from the repository you are working in.
- Do not paste secrets, credentials or customer personal data into issues or
  comments. Issues are visible to the customer's users.
