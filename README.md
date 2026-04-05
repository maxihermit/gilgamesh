# 王之開源（Gate of Open Source）

![banner](assets/banner.png)

[繁體中文](#這是什麼) | [English](#english)

> *「你竟敢在沒有查閱寶庫的情況下開發？天下所有的開源寶物皆屬於我。」*

## 這是什麼

你是不是每次都在 Twitter 或 Hacker News 上偶然看到別人分享了一個超讚的開源工具，然後才發現「靠，這個東西三個月前就有了，我到底在幹嘛」？

這個 AI Skill 幫你解決這個問題。裝好之後打 `/oss`，它會：

1. **先看懂你的代碼** — 不是隨便看 package.json 就完事，是真的讀你的代碼理解架構
2. **找出真正的缺口** — 哪裡寫得好不用動，哪裡有問題才需要找工具
3. **針對缺口搜 GitHub + Hacker News** — 只推薦能解決實際問題的
4. **做資安檢查** — 授權條款、已知漏洞、有沒有人維護

**它只會給你報告，不會幫你安裝任何東西。畢竟王只鑑定，不親自動手。**

## 跟其他「找開源」的工具有什麼不同

大多數推薦工具的做法是：看你用了 React → 推薦 React 生態的熱門庫。

問題是：**熱門不代表你需要。**

這個工具的做法是：先讀你的代碼 → 發現你的 HTTP 重試邏輯其實寫得很好 → 不推薦 axios，因為你不需要。

**「你目前不需要任何東西」是一個完全正常且有價值的結果。**

## 它怎麼運作

```
第一次 /oss（花一點時間，但只需一次）
      ↓
讀你的代碼，理解架構和依賴
      ↓
標記哪些已經很好（不推薦替換）
標記哪些有真正的缺口
      ↓
把分析結果存進 .oss-profile.yml
      ↓
只針對缺口搜 GitHub + Hacker News
      ↓
檢查安全性 + 評估遷移成本
      ↓
給你鑑定結果（按缺口分組）

之後每次 /oss
      ↓
讀 .oss-profile.yml（已有分析結果）
      ↓
檢查 git diff 有沒有大改
      ↓
沒大改 → 直接搜（省 token）
有大改 → 只重掃變動部分
```

### 鑑定等級

| 等級 | 意思 |
|------|------|
| SSR | 直接解決你代碼裡的真實問題，安全，遷移成本合理 |
| SR | 值得花時間試用 |
| R | 有潛力，先加個書籤 |
| N | 有疑慮，先別碰 |
| 廢鐵 | 不安全，狗都不用 |

## 安裝

一行指令，裝完就能用：

### Claude Code

```bash
curl -o ~/.claude/commands/oss.md https://raw.githubusercontent.com/maxihermit/gate-of-oss/main/oss.md
```

### OpenAI Codex

```bash
mkdir -p .codex/skills/oss
curl -o .codex/skills/oss/SKILL.md https://raw.githubusercontent.com/maxihermit/gate-of-oss/main/oss.md
```

### Cursor

```bash
mkdir -p .cursor/rules
curl -o .cursor/rules/oss.mdc https://raw.githubusercontent.com/maxihermit/gate-of-oss/main/oss.md
```

### GitHub Copilot

把 `oss.md` 的內容**追加**到 `.github/copilot-instructions.md` 的最後面。

### 其他平台

直接把 `oss.md` 的內容貼到對話裡，一樣能用。

## 怎麼用

```
/oss                          # 幫你找有用的新工具（第一次會先分析代碼）
/oss all                      # 你有 5 個專案？一次全掃
/oss audit owner/repo         # 老闆說要用這個套件，先幫我查一下會不會爆炸
/oss typescript               # 只看特定語言的
/oss 簡報工具                   # 我要做簡報，有沒有什麼好東西
/oss init                     # 告訴它你的專案在做什麼，推薦會更準
/oss rescan                   # 強制重新分析代碼（大改版後用）
```

### 每天自動跑

在 Claude Code 裡說：

```
幫我建一個排程任務，每天早上跑 /oss all
```

從此每天早上打開電腦就有一份鑑定報告等你。比看新聞有用多了。

## 報告長什麼樣子

```
我的寶庫裡有幾個東西可能適合 my-web-app：

═══════════════════════════════════════
  my-web-app — 電商網站
  代碼掃描：2025-01-15 | 缺口：1 個
═══════════════════════════════════════

  缺口 1：AI 串流回應手寫 80 行，不穩定
  ─────────────────────────────────────
  SSR  ⭐ 15,234  vercel/ai — 3 行就能做串流 AI 回應

  ┌─ vercel/ai — 做 AI 功能的 SDK
  │  ⭐ 15,234  MIT  2 天前更新  156 人貢獻
  │  SSR
  │
  │  你現在的代碼：自己寫 fetch 串 OpenAI，80 行，串流斷了沒重試
  │  用了之後：用 useChat hook，3 行，自動串流 + 重試
  │  值不值得：省 80 行 + 解決穩定性問題，遷移 30 分鐘
  │
  │  安全嗎：MIT ✓ | 漏洞: 0 | 有人維護 ✓
  └─────────────────────────────────────
```

沒有缺口的時候：
```
═══════════════════════════════════════
  my-web-app
  代碼掃描完成，目前沒有明顯缺口。繼續保持。
═══════════════════════════════════════
```

## 資安與隱私

- **會讀你的代碼**來理解架構（這是推薦準確的關鍵）
- **不讀** .env、密碼、金鑰
- **不安裝**任何東西，只給你報告
- **不會把你的代碼傳到外面**（代碼分析在本地，只有套件名稱會送到 GitHub/OSV API）
- 分析結果存在你的 `.oss-profile.yml`，你可以隨時刪掉
- 規則全寫在 `oss.md` 裡，你可以自己看完每一行

## GITHUB_TOKEN（選用）

不設也能用，但一小時只能搜 60 次。設了可以搜 5,000 次。如果你每天自動跑的話建議設一下。

```bash
export GITHUB_TOKEN=ghp_...
```

---

## English

> *"You dare develop without consulting the treasury? All the world's open-source treasures belong to me."*

An AI skill that **reads your actual code first**, finds real gaps, then searches GitHub and Hacker News for libraries that solve those specific problems. Security-checked.

**It only reports. It never installs anything.**

### What makes this different

Most "find open-source" tools look at your stack and recommend popular libraries.

This one reads your code, understands your architecture, and only recommends things that solve **verified problems**. "You don't need anything right now" is a perfectly valid result.

### How it works

1. First run: analyzes your code → saves findings to `.oss-profile.yml`
2. Subsequent runs: reads cached analysis → checks git diff → searches only for real gaps
3. Results grouped by gap, not by popularity

### Install

**Claude Code:**
```bash
curl -o ~/.claude/commands/oss.md https://raw.githubusercontent.com/maxihermit/gate-of-oss/main/oss.md
```

**Codex:** `mkdir -p .codex/skills/oss && curl -o .codex/skills/oss/SKILL.md https://raw.githubusercontent.com/maxihermit/gate-of-oss/main/oss.md`

**Cursor:** `mkdir -p .cursor/rules && curl -o .cursor/rules/oss.mdc https://raw.githubusercontent.com/maxihermit/gate-of-oss/main/oss.md`

**Copilot:** Append `oss.md` contents to `.github/copilot-instructions.md`.

### Usage

```
/oss                          # Find tools for verified gaps (analyzes code first time)
/oss all                      # Scan all your projects at once
/oss audit owner/repo         # "Boss wants this lib, check if it'll blow up"
/oss typescript               # Filter by language
/oss presentation tools       # Search specific topic
/oss init                     # Describe your project for better results
/oss rescan                   # Force full code re-analysis
```

### Daily auto-scan

In Claude Code: `Help me create a scheduled task that runs /oss all every morning`

### Grades

| Grade | Meaning |
|-------|---------|
| SSR | Solves a verified gap in your code. Safe. Migration cost justified. |
| SR | Worth trying for a real gap. |
| R | Has potential. Bookmark it. |
| N | Has concerns. Don't touch it yet. |
| Junk | Unsafe. Not even my dog would use it. |

### Security & Privacy

- **Reads your code** to understand architecture (this is why recommendations are accurate)
- Never reads `.env`, passwords, API keys
- Never installs anything (that's your call)
- Code analysis stays local — only package names go to GitHub/OSV APIs
- Analysis cached in `.oss-profile.yml` (deletable anytime)
- Full prompt in `oss.md` — read every line yourself

## License

MIT

---

*如果你笑出來了，請給一顆星星。If this made you laugh, please star it.*
