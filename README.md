# 4d-bag（四次元百寶袋）

[English](#what-it-does) | [繁體中文](#繁體中文說明)

An AI skill that finds trending open-source projects and tells you which ones are worth using in your projects — with security checks. Works with Claude Code, Codex, Cursor, Copilot, or any AI assistant.

**It only reports. It never installs anything.**

## What it Does

```
You have projects in development
        ↓
Every day, new open-source tools appear on GitHub
You can't check them all manually
        ↓
4d-bag searches GitHub + Hacker News for you
        ↓
Compares against YOUR project's tech stack
        ↓
Checks security: license, CVEs, maintenance health
        ↓
Gives you a report: ADOPT / TRIAL / ASSESS / HOLD / AVOID
```

## Install

### Claude Code (recommended)

```bash
# Option 1: Global (works in all projects)
curl -o ~/.claude/commands/4d-bag.md https://raw.githubusercontent.com/maxihermit/4d-bag/main/4d-bag.md

# Option 2: Per-project
mkdir -p .claude/commands
curl -o .claude/commands/4d-bag.md https://raw.githubusercontent.com/maxihermit/4d-bag/main/4d-bag.md
```

Then use:
```
/4d-bag init                     # first time: create project profile
/4d-bag                          # scan current project
/4d-bag all                      # scan all projects
/4d-bag audit owner/repo         # security audit
/4d-bag typescript               # filter by language
/4d-bag presentation tools       # search specific topic
```

### OpenAI Codex / Cursor / Copilot / Others

Copy the contents of `4d-bag.md` into your platform's instruction file:

| Platform | Copy to |
|----------|---------|
| Codex | `AGENTS.md` in your repo |
| Cursor | `.cursorrules` in your repo |
| Copilot | `.github/copilot-instructions.md` |
| Others | Paste into the system prompt or conversation |

### Scheduled (daily auto-scan)

In Claude Code, set up a scheduled task to run `/4d-bag all` every morning. It will scan all your projects and notify you of new tools worth checking out.

## Example Output

```
## Your Project: my-web-app
Tech stack: TypeScript, React, Next.js, Prisma

### vercel/ai — AI SDK for building AI-powered apps
⭐ 15,234 | MIT | active (2 days ago) | 156 contributors
ADOPT
Why: You're already using Next.js. This gives you streaming AI responses with 3 lines of code.
Security: MIT license, no CVEs, actively maintained.

### shadcn/ui — UI components built on Radix
⭐ 82,000 | MIT | active (today) | 400+ contributors
TRIAL
Why: You're using Tailwind but building components from scratch. This saves time.
Security: MIT, no CVEs, very active.
```

## Security & Privacy

- **Only reads** package.json, requirements.txt, etc. — never your source code
- **Never reads** .env, credentials, API keys, or secrets
- **Never installs** anything — only reports findings
- **Never sends** your code to external APIs — only searches GitHub/HN/OSV
- All API calls go to public endpoints (GitHub, Hacker News, OSV.dev)
- Full prompt is in `4d-bag.md` — read it yourself, it's 90 lines

## Optional: GITHUB_TOKEN

Without it: 60 GitHub API requests/hour (enough for occasional use).
With it: 5,000/hour (needed for daily scans).

```bash
export GITHUB_TOKEN=ghp_...
```

---

## 繁體中文說明

一個 AI Skill，自動幫你找最近熱門的開源專案，對比你正在開發的所有專案，告訴你哪些值得用、哪些有資安問題。

**只看不裝，不讀你的原始碼。**

### 安裝

```bash
# Claude Code
curl -o ~/.claude/commands/4d-bag.md https://raw.githubusercontent.com/maxihermit/4d-bag/main/4d-bag.md

# 其他平台（Codex / Cursor / Copilot）
# 把 4d-bag.md 的內容複製到對應的設定檔裡
```

### 使用方式

```
/4d-bag init                     # 第一次用：建立專案描述檔
/4d-bag                          # 掃描目前的專案
/4d-bag all                      # 掃描所有專案
/4d-bag audit owner/repo         # 資安審計指定 repo
/4d-bag typescript               # 只看特定語言
/4d-bag 簡報工具                   # 搜尋特定主題
```

### 它會做什麼

1. 第一次���用時會問你 3 個問題（專案做什麼、痛點、感興趣的主題），存成 `.4dbag-profile.yml`
2. 之後每次自動讀取��個檔案，不浪費 token
3. 搜尋 GitHub + Hacker News 熱門專案
4. 根據你的痛點和興趣評估相關性
5. 檢查 license、已知漏洞、維護狀態
6. 給出建議：ADOPT（直接用）/ TRIAL（試試看）/ ASSESS（觀望）/ HOLD（先別碰）/ AVOID（別用）

可以搭配排程任務每天自動跑。

### 資安與隱私

- 只讀 package.json 等設定檔，**不讀原始碼**
- **不讀** .env、密碼、金鑰等機敏檔案
- **不安裝**任何東西，只產生報告
- **不會把你的程式碼傳到外部**，只查詢 GitHub / HN / OSV 公開 API
- 完整 prompt 就在 `4d-bag.md` 裡，90 行，你可以自己審查

## License

MIT
