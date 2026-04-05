# 四次元百寶袋（4D Bag）

![banner](assets/banner.png)

[繁體中文](#這是什麼) | [English](#english)

## 這是什麼

一個 AI Skill。裝好之後，你只要打 `/oss`，它就會像從百寶袋裡翻東西一樣，自動去 GitHub 和 Hacker News 幫你找最近熱門的開源專案，跟你說哪些對你正在開發的東西有用，然後幫你檢查那些專案安不安全。

**它只會給你報告，不會幫你安裝任何東西。**

## 它怎麼運作

```
你正在開發專案
      ↓
每天 GitHub 上都有新的開源工具出現
你不可能每天自己去看
      ↓
/oss 幫你去翻百寶袋
      ↓
比對你的專案用了什麼技術、缺了什麼
      ↓
檢查安全性：授權條款、已知漏洞、有沒有人在維護
      ↓
給你鑑定結果：SSR / SR / R / N / 廢鐵
```

### 鑑定等級

| 等級 | 意思 |
|------|------|
| SSR | 直接解決你的問題，安全，馬上用 |
| SR | 值得試用 |
| R | 有潛力，再看看 |
| N | 有疑慮，先別碰 |
| 廢鐵 | 不安全，別用 |

## 安裝

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

直接把 `oss.md` 的內容貼到對話裡。

## 怎麼用

```
/oss init                     # 第一次用：告訴它你的專案在做什麼
/oss                          # 幫你找有用的新工具
/oss all                      # 一次掃描你所有的專案
/oss audit owner/repo         # 檢查某個 repo 安不安全
/oss typescript               # 只看特定語言的
/oss 簡報工具                   # 搜特定主題
```

### 每天自動跑

在 Claude Code 裡說：

```
幫我建一個排程任務，每天早上跑 /oss all
```

每天早上自動收到報告。

## 報告長什麼樣子

```
我去翻了一下 GitHub，幫 my-web-app 找到幾個好東西：

🔮 my-web-app — 電商網站

  SSR  ⭐ 15,234  vercel/ai — 3 行就能做串流 AI 回應
  SR   ⭐ 82,000  shadcn/ui — 不用再自己刻 UI 元件

  ┌─ 從百寶袋裡翻到：vercel/ai
  │  ⭐ 15,234  MIT  2 天前更新  156 人貢獻
  │  SSR
  │
  │  你現在：自己寫 fetch 串 OpenAI，80 行，串流不穩定
  │  用了之後：用 useChat hook，3 行，自動串流 + 重試
  │  差在哪：省 80 行程式碼，穩定性大幅提升
  │
  │  安全嗎：MIT ✓ | 漏洞: 0 | 有人維護 ✓
  └─────────────────────────────────────
```

## 資安與隱私

- 只讀 package.json 這類設定檔，**不讀你的程式碼**
- **不讀** .env、密碼、金鑰
- **不安裝**任何東西，只給你報告
- **不會把你的程式碼傳到外面**
- 規則全寫在 `oss.md` 裡，你可以自己看完每一行

## GITHUB_TOKEN（選用）

不設也能用，但一小時只能搜 60 次。設了可以搜 5,000 次。

```bash
export GITHUB_TOKEN=ghp_...
```

---

## English

An AI skill that searches GitHub and Hacker News for trending open-source projects, tells you which ones are useful for your project, and checks if they're safe to use.

**It only reports. It never installs anything.**

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
/oss init                     # First time: describe your project
/oss                          # Find useful new tools
/oss all                      # Scan all your projects at once
/oss audit owner/repo         # Security check a specific repo
/oss typescript               # Filter by language
/oss presentation tools       # Search specific topic
```

### Daily auto-scan

In Claude Code, say: `Help me create a scheduled task that runs /oss all every morning`

### Grades

| Grade | Meaning |
|-------|---------|
| SSR | Solves your problem. Safe. Use it. |
| SR | Worth trying. |
| R | Has potential. Evaluate further. |
| N | Has concerns. Wait. |
| Junk | Unsafe. Don't use it. |

### Security & Privacy

- Only reads config files (package.json, etc.) — never your source code
- Never reads .env, passwords, API keys
- Never installs anything
- Never sends your code anywhere
- Full prompt is in `oss.md` — read it yourself

## License

MIT

---

*如果你笑出來了，請給一顆星星。If this made you laugh, please star it.*
