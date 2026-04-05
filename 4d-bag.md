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
- `/4d-bag init` — create or update project profile

## Step 1: Understand the Project

**Check if `.4dbag-profile.yml` exists in the project root.** This file contains the user's own description of their project, saving tokens and giving better results than reading source code.

**If `.4dbag-profile.yml` exists:** use it directly. Skip to Step 2.

**If it does NOT exist:** read `package.json`, `requirements.txt`, `pyproject.toml`, `go.mod`, `Cargo.toml`, or `README.md` to detect the tech stack. Then ask the user:

> I detected your project uses [tech stack]. To give you better recommendations, I'd like to create a `.4dbag-profile.yml`. Can you briefly tell me:
> 1. What does this project do? (one sentence)
> 2. What are your current pain points or things you wish were easier?
> 3. Any topics you're especially interested in? (e.g. testing, UI, auth, performance)

Save the answers as `.4dbag-profile.yml`:

```yaml
name: my-project
description: E-commerce app with user auth and payment
stack: [typescript, react, nextjs, prisma, tailwind]
pain_points: [auth is hand-rolled and fragile, no proper testing setup]
interests: [auth, testing, ui-components, performance]
exclude: [crypto, blockchain, game-dev]
```

**If command is `init`:** always ask the user these questions and create/update the profile, even if one already exists.

**If command is `all`:** check each subdirectory for `.4dbag-profile.yml`. For directories without one, detect stack from manifest files and ask once for all missing profiles.

**NEVER read source code, .env, credentials, API keys, or secrets.**

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
If the profile has `interests`, also search for repos matching those topics.

## Step 3: Evaluate

Use the project profile (description, pain_points, interests) to judge relevance. Only recommend repos that genuinely help. **Zero is better than noise.**

- ADOPT — clearly solves a pain point the user mentioned + safe
- TRIAL — likely useful for their interests, worth trying
- ASSESS — potential but unclear fit
- HOLD — has concerns (security, maintenance)
- AVOID — unsafe or unsuitable

If the profile has `exclude` topics, skip repos in those categories.

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
[description from profile]
Tech stack: ...
Pain points: ...

### owner/repo — what it does
⭐ N stars | MIT | active | N contributors
**ADOPT**
Why: [specifically reference the user's pain point or interest this solves]
Security: one sentence
```

## Rules

- **NEVER install anything** — only report
- **NEVER read** source code, .env, credentials, secrets
- **NEVER output** API keys or tokens
- If GitHub returns 403/429, tell user to set GITHUB_TOKEN
- Skip archived and unlicensed repos
- Output in user's language
