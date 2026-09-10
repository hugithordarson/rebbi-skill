# rebbi-skill

A [Claude Code](https://claude.com/claude-code) skill for tracking development work
as issues in [Rebbi](https://www.godurkodi.is), through its JSON API.

Agents working in a project that Rebbi tracks can register issues, move them across
the board and record findings as comments, so the board keeps up with the work
without anyone retyping it.

## Install

Into a single project:

```sh
git clone https://github.com/hugithordarson/rebbi-skill .claude/skills/rebbi
```

Or for every project, into your personal skills directory:

```sh
git clone https://github.com/hugithordarson/rebbi-skill ~/.claude/skills/rebbi
```

Update with `git pull` in that directory.

## Token

The skill reads a bearer token from `~/.config/rebbi/api-token`:

```sh
mkdir -p ~/.config/rebbi
printf '%s' "<token>" > ~/.config/rebbi/api-token
chmod 600 ~/.config/rebbi/api-token
```

Tokens are minted from the database and do not expire; they are revoked by setting
the login row's `logout_date`. The token never belongs in a repository.

## What is in here

`SKILL.md` — the instructions an agent reads: how to connect, the calls, and the
conventions for writing issues.

The API's own reference, with every parameter and response shape, is `API.md` in the
Rebbi repository, next to the code that implements it.
