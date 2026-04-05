---
name: 4d-bag
description: Discover trending open-source projects, evaluate relevance to your project, and run security audits
---

User command: $ARGUMENTS

# What to Do

Discover trending open-source projects and tell the user which ones are useful for their project. **Only report. Never install anything.**

## Commands

- `/4d-bag` — scan current project
- `/4d-bag all` — scan all projects in working directory
- `/4d-bag audit owner/repo` — security audit one repo
- `/4d-bag typescript` — filter by language
- `/4d-bag daily` — last 24 hours (default: weekly)
- `/4d-bag presentation tools` — search specific topic
- `/4d-bag deep` — deep mode: also read source code for better recommendations

## Step 1: Read the Project

**Default mode:** Read `package.json`, `requirements.txt`, `pyproject.toml`, `go.mod`, `Cargo.toml`, or `README.md` to understand the tech stack. This is safe and enough for most cases.

**Deep mode** (user must explicitly say `deep`): Also scan source code files (*.ts, *.py, *.go, etc.) to understand what the project actually does — e.g. detect hand-rolled auth, custom ORMs, or pain points that manifest files don't reveal. Only use this when the user explicitly requests it.

If `all`: scan every subdirectory.

**In both modes: NEVER read .env, credentials, API keys, or secrets.**

## Step 2: Search

```bash
# GitHub — trending repos (adjust date/language as needed)
curl -s "https://api.github.com/search/repositories?q=pushed:>$(date -d '7 days ago' +%Y-%m-%d)+stars:>100&sort=stars&order=desc&per_page=25" \
  -H "Accept: application/vnd.github.v3+json" -H "User-Agent: 4d-bag" \
  ${GITHUB_TOKEN:+-H "Authorization: token $GITHUB_TOKEN"}
```

```bash
# Hacker News — GitHub repos with 20+ upvotes
curl -s "https://hn.algolia.com/api/v1/search?query=github.com&tags=story&numericFilters=created_at_i>$(date -d '7 days ago' +%s),points>20&hitsPerPage=20"
```

If user specified a topic (e.g. "presentation tools"), add it to the GitHub search query.

## Step 3: Evaluate

Only recommend repos that genuinely help the user's project. **Zero is better than noise.**

- ADOPT — clearly useful + safe
- TRIAL — likely useful, worth trying
- ASSESS — potential but unclear
- HOLD — has concerns
- AVOID — unsafe or unsuitable

## Step 4: Security Check

For each recommendation:

```bash
# License (safe = MIT, Apache-2.0, BSD, ISC)
curl -s "https://api.github.com/repos/OWNER/REPO/license" -H "Accept: application/vnd.github.v3+json" -H "User-Agent: 4d-bag" ${GITHUB_TOKEN:+-H "Authorization: token $GITHUB_TOKEN"}

# Maintenance (check pushed_at, archived)
curl -s "https://api.github.com/repos/OWNER/REPO" -H "Accept: application/vnd.github.v3+json" -H "User-Agent: 4d-bag" ${GITHUB_TOKEN:+-H "Authorization: token $GITHUB_TOKEN"}

# CVE (for npm deps)
curl -s -X POST "https://api.osv.dev/v1/query" -H "Content-Type: application/json" -d '{"package":{"name":"PKG","ecosystem":"npm"},"version":"VER"}'
```

Red flags: no license, archived, last push > 1 year, single contributor.

## Step 5: Report

```
## Your Project: [name]
Tech stack: ...

### owner/repo — what it does
⭐ N stars | MIT | active | N contributors
**ADOPT**
Why: one sentence
Security: one sentence
```

## Rules

- **NEVER install anything** — only report
- **NEVER read** source code, .env, credentials, secrets
- **NEVER output** API keys or tokens
- If GitHub returns 403/429, tell user to set GITHUB_TOKEN
- Skip archived and unlicensed repos
- Output in user's language
