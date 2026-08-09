# Hermes_Agent

## 1. 架構

建議先採用：

```sh
Windows 10 / 11
│
├── PowerShell / Windows Terminal
│
├── Hermes Agent
│ ├── Python
│ ├── Node.js
│ ├── ripgrep
│ └── ffmpeg
│
├── LLM Provider
│ ├── OpenRouter
│ ├── Nous Portal
│ ├── OpenAI
│ ├── Anthropic
│ └── Local LLM
│
└── Tools / Skills / MCP / Gateway
```

官方安裝程式會處理 Python、Node.js、ripgrep、ffmpeg、Git repository、虛擬環境以及 Hermes 指令等依賴。

---

## 2. Windows 基礎環境

建議：

| 項目       | 建議              |
| ---------- | ----------------- |
| OS         | Windows 11 64-bit |
| RAM        | 16 GB ↑           |
| CPU        | 4 Core ↑          |
| SSD        | 20 GB ↑           |
| 網路       | 必須              |
| PowerShell | 5.1+              |
| GPU        | 非必要            |
| WSL2       | 不需要            |

如果你只是要跑 Agent + API，GPU 不是必要條件。

如果之後要接你自己的 Ollama / vLLM / GPU LLM，才需要考慮 GPU。

---

## 3. 安裝 Hermes Agent

開啟：

Windows Terminal → PowerShell

執行官方 Windows installer：

```sh
iex (irm https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.ps1)
```

官方目前也提供：

[Hermes Agent 官方 Windows 安裝文件](https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/windows-native.md?utm_source=chatgpt.com)

安裝程式預設會放在：

```sh
%LOCALAPPDATA%\hermes\
```

例如：

```sh
C:\Users\你的帳號\AppData\Local\hermes\
```

並且會把 `hermes` 加到 User PATH。

---

## 4. 重新開啟 PowerShell

這一步很重要。

安裝完成後：

```
關閉目前 PowerShell
↓
重新開 PowerShell
↓
確認 PATH
```

執行：

```sh
Get-Command hermes
```

正常應該看到類似：

```
C:\Users\<User>\AppData\Local\hermes\hermes-agent\venv\Scripts\hermes.exe
```

再確認版本：

```sh
hermes --version
```

官方文件也特別提醒，舊的 Terminal 不會自動取得新的 User PATH。

---

## 5. 第一次初始化

執行：

```sh
hermes setup
```

這會進入 Hermes 的設定流程。

也可以直接使用：

```sh
hermes
```

開始 Agent。

---

## 6. 設定 LLM Provider

Hermes 本身不是 LLM。

架構是：

```sh
Hermes Agent
│
▼
LLM Provider
│
▼
AI Model
```

例如：

```sh
Hermes
│
├── OpenRouter
│ └── Claude / GPT / Qwen / Gemini...
│
├── OpenAI
│ └── GPT
│
├── Anthropic
│ └── Claude
│
├── Nous Portal
│
└── Local
└── Ollama / vLLM
```

官方提供：

```sh
hermes model
```

讓你選擇模型 Provider / Model。

---

## 7. 如果使用 Nous Portal

目前官方推薦的一條快速路徑是：

```
hermes setup --portal
```

它會進行登入、Provider 設定以及 Tool Gateway 啟用。

如果你想最快測試 Hermes，我會建議：

```sh
Windows
↓
Hermes
↓
Nous Portal
↓
先確認 Agent 正常
```

確認成功後，再改成其他 Provider。

---

## 8. API Key 設定

Windows 原生 Hermes 的資料目錄：

```sh
%LOCALAPPDATA%\hermes
```

其中可以放：

```sh
%LOCALAPPDATA%\hermes\.env
```

例如：

```sh
OPENROUTER_API_KEY=你的KEY
```

或其他 Provider 所需要的 API Key。

官方建議使用 Hermes 自己的 .env 保存 API Key，而不是把秘密直接放到 Windows User Environment Variables，避免所有 Windows 程式都能看到。

---

## 9. 測試 Hermes

完成設定後：

```sh
hermes
```

然後測試：

```sh
你好，請介紹你自己。
```

再測試 Agent 能力：

```sh
請列出目前工作目錄的檔案。
```

如果可以執行工具，代表基本環境 OK。

---

## 10. 測試 Tool

可以使用：

```sh
hermes tools
```

查看 Tool 設定。

建議至少確認：

```sh
✓ File access
✓ Shell / command execution
✓ Web access
✓ Browser
✓ Code execution
```

實際可用項目會依 Hermes 版本與 Windows 支援狀態而有所不同。

---

## 11. Windows Gateway

如果你的目標不是單純：

```sh
Terminal
↓
Hermes
```

而是想做：

```sh
LINE / Telegram / Discord
↓
Hermes
↓
Agent
↓
Tools
```

可以進一步設定 Gateway。

官方提供：

```sh
hermes gateway setup
```

Windows 原生也可以把 Gateway 設定成 Windows Scheduled Task，讓它在背景執行。

---

## 12. 建議的正式架構

如果你是要把 Hermes 當成「長期運作的 AI Agent」，我比較建議：

```sh
                 Internet
                    │
          ┌─────────┴─────────┐
          │                   │
      Telegram             Web/API
          │                   │
          └─────────┬─────────┘
                    │
                    ▼
            ┌──────────────┐
            │ Hermes Agent │
            │   Windows    │
            └──────┬───────┘
                   │
       ┌───────────┼───────────┐
       │           │           │
       ▼           ▼           ▼
     LLM          Tools       Skills
       │           │           │
       ▼           ▼           ▼

OpenRouter Shell/File Custom
OpenAI Browser Skills
Local LLM Git MCP
```

---

## 13. 如果你要接本機 AI

這個部分其實很適合你的環境。

你之前有研究 RTX 5090 + 大記憶體 + LLaVA / 本地 LLM，如果這台 Windows 電腦就是你的 AI 主機，可以做成：

```sh
Windows
│
├── Hermes Agent
│
├── Ollama / vLLM
│ │
│ └── Local LLM
│
├── RTX 5090
│
├── OpenCV
│
├── Python
│
└── Git Repository
```

變成：

```sh
Hermes
│
▼
Local LLM
│
├── 讀檔案
├── 寫程式
├── 執行 Python
├── Git
├── OpenCV
└── 自動化工作
```

這會比單純把 Hermes 當 ChatGPT CLI 更有價值。

---

## 14. Windows 原生 vs WSL2

這點值得特別注意。
| 項目 | Windows Native | WSL2 |
| ------------------ | -------------: | ----: |
| 安裝難度 | ⭐ | ⭐⭐ |
| Windows 程式 | ★★★★★ | ★★★ |
| PowerShell | ★★★★★ | ★★ |
| Linux CLI | ★★ | ★★★★★ |
| POSIX | ★★ | ★★★★★ |
| Git | ★★★★★ | ★★★★★ |
| Windows 自動化 | ★★★★★ | ★★★ |
| Dashboard Terminal | 部分限制 | ★★★★★ |
| 適合 Windows Agent | **★★★★★** | ★★★★ |

官方目前已支援 Windows 10/11 原生執行；如果需要完整 POSIX/Linux 行為，例如 Dashboard embedded terminal、fork 語義、Linux filesystem watcher 等，官方仍建議考慮 WSL2。兩種模式可以共存，而且資料目錄彼此獨立。

---

## 15. 我建議你的 SOP

如果你的目的是真正架一台 Hermes Agent Windows 工作站，不要一次把所有功能都裝上去。

按照：

```
Phase 1
Windows
↓
Hermes
↓
LLM Provider
↓
基本對話
```

↓

```
Phase 2
Hermes
↓
Tools
↓
Shell
↓
File
↓
Git
```

↓

```
Phase 3
Hermes
↓
Browser
↓
Web
↓
MCP
```

↓

```
Phase 4
Hermes
↓
Gateway
↓
Telegram / Discord
```

↓

```
Phase 5
Hermes
↓
Local LLM
↓
RTX 5090
```

這樣比較容易排錯，也比較適合正式部署。
