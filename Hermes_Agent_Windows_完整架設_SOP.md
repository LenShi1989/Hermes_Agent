# Hermes Agent Windows 架設 SOP

# 1. 目標

建立一套可在 Windows 10/11 原生執行的 Hermes Agent 環境，包含：

- Hermes Agent
- Python / uv
- Node.js
- Git / Git Bash
- ffmpeg / ripgrep
- LLM Provider
- Shell / File / Browser Tools
- MCP
- Gateway
- Telegram / Discord 等訊息平台
- Windows Scheduled Task 自動啟動
- 後續可接本機 LLM / Ollama / RTX 5090

---

# 2. 架構

```text
Windows 10 / 11
│
├─ Hermes Agent
│   ├─ Python 3.11
│   ├─ Node.js 22
│   ├─ Git / Git Bash
│   ├─ ripgrep
│   └─ ffmpeg
│
├─ LLM Provider
│   ├─ Nous Portal
│   ├─ OpenRouter
│   ├─ OpenAI
│   ├─ Anthropic
│   └─ Local LLM
│
├─ Tools
│   ├─ Shell
│   ├─ File
│   ├─ Browser
│   ├─ Git
│   ├─ MCP
│   └─ Web
│
├─ Gateway
│   ├─ Telegram
│   ├─ Discord
│   ├─ Slack
│   └─ WhatsApp
│
└─ Windows Scheduled Task
       ↓
   開機/登入自動啟動
```

---

# 3. Windows 環境確認

## 3.1 開啟 PowerShell

建議使用：

```text
Windows Terminal → PowerShell
```

## 3.2 確認 Windows

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsArchitecture
```

建議：

- Windows 10 / 11
- 64-bit

## 3.3 確認 PowerShell

```powershell
$PSVersionTable
```

如果已安裝 PowerShell 7：

```powershell
pwsh
```

---

# 4. 安裝 Hermes Agent

## 4.1 官方 Windows 安裝

PowerShell 執行：

```powershell
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

官方 Windows Native Installer 會處理 Hermes 所需的主要依賴。

通常不需要 Administrator 權限。

預設安裝資料夾：

```text
%LOCALAPPDATA%\hermes
```

也就是：

```text
C:\Users\<你的帳號>\AppData\Local\hermes
```

---

# 5. 重新開啟 PowerShell

安裝完成後：

```text
關閉目前 PowerShell
↓
重新開啟 Windows Terminal
↓
開新的 PowerShell
```

原因是 Installer 會修改 User PATH，已開啟的 PowerShell 不一定會立即取得新的 PATH。

---

# 6. 確認 Hermes

## 6.1 PATH

```powershell
Get-Command hermes
```

## 6.2 版本

```powershell
hermes --version
```

## 6.3 系統檢查

```powershell
hermes doctor
```

---

# 7. 確認 Hermes 目錄

```powershell
Get-ChildItem "$env:LOCALAPPDATA\hermes"
```

常見資料：

```text
%LOCALAPPDATA%\hermes
│
├── hermes-agent
├── git
├── node
├── bin
├── config.yaml
├── .env
├── skills
├── sessions
└── logs
```

---

# 8. 確認 Git

```powershell
git --version
```

```powershell
where.exe git
```

如果系統沒有 Git，官方 Installer 會配置 Portable Git。

---

# 9. 確認 Node.js

```powershell
node --version
```

```powershell
npm --version
```

官方 Windows Installer 使用 Node.js 22。

---

# 10. 確認 Python / uv

```powershell
python --version
```

```powershell
uv --version
```

Hermes 使用 uv 管理 Python 環境，官方安裝流程會配置 Python 3.11。

---

# 11. 執行 Hermes Setup

```powershell
hermes setup
```

常用設定指令：

```powershell
hermes model
```

```powershell
hermes tools
```

```powershell
hermes gateway setup
```

```powershell
hermes config
```

---

# 12. Nous Portal 設定

如果使用 Nous Portal：

```powershell
hermes setup --portal
```

完成登入、Provider、Model 等設定後：

```powershell
hermes
```

測試：

```text
你好
```

再測試：

```text
請告訴我目前可以使用哪些工具。
```

---

# 13. 啟動 Hermes

```powershell
hermes
```

基本測試：

```text
你好，請介紹你自己。
```

---

# 14. 測試 Shell Tool

在 Hermes 中：

```text
請列出目前 Windows 工作目錄的檔案。
```

如果 Agent 能呼叫 Windows Shell，代表基本 Tool 正常。

---

# 15. 測試檔案操作

建立測試資料夾：

```powershell
New-Item -ItemType Directory -Path "$env:USERPROFILE\HermesTest" -Force
```

建立測試檔案：

```powershell
Set-Content "$env:USERPROFILE\HermesTest\test.txt" "Hello Hermes"
```

在 Hermes 中：

```text
請讀取 C:\Users\<你的帳號>\HermesTest\test.txt
```

---

# 16. 測試 Git

```powershell
Set-Location "$env:USERPROFILE\HermesTest"
```

```powershell
git init
```

```powershell
Set-Content README.md "# Hermes Test"
```

```powershell
git add .
```

```powershell
git commit -m "initial commit"
```

在 Hermes 中：

```text
查看這個 Git repository 的狀態。
```

---

# 17. Browser Tool

確認：

```powershell
Get-Command agent-browser
```

第一次使用 Browser Tool 時，可能會自動處理 Playwright Chromium。

再執行：

```powershell
hermes doctor
```

確認 Browser 狀態。

---

# 18. MCP

Hermes 支援 MCP：

```text
Hermes
 │
 ├── MCP stdio
 │
 └── MCP HTTP
```

查看 Tool：

```powershell
hermes tools
```

再依實際 MCP Server 進行設定。

---

# 19. Gateway

如果要讓：

```text
Telegram
Discord
Slack
WhatsApp
```

連接 Hermes：

```powershell
hermes gateway setup
```

Gateway 架構：

```text
Telegram / Discord / Slack / WhatsApp
                    │
                    ▼
             Hermes Gateway
                    │
                    ▼
              Hermes Agent
```

---

# 20. Telegram

先取得 Telegram Bot Token。

設定 Hermes `.env`：

```powershell
$HermesHome = "$env:LOCALAPPDATA\hermes"
```

確認：

```powershell
Test-Path "$HermesHome\.env"
```

如果不存在：

```powershell
New-Item "$HermesHome\.env" -ItemType File -Force
```

編輯：

```powershell
notepad "$HermesHome\.env"
```

加入：

```env
TELEGRAM_BOT_TOKEN=你的TelegramBotToken
```

不要把 Token 提交到 Git repository。

---

# 21. 啟動 Gateway

手動測試：

```powershell
hermes gateway
```

確認 Telegram / 其他平台可以連線到 Hermes。

---

# 22. Windows 自動啟動

官方 Windows Native Gateway 可以使用 Windows Scheduled Task。

執行：

```powershell
hermes gateway install
```

---

# 23. 查看 Scheduled Task

```powershell
Get-ScheduledTask -TaskName "HermesGateway"
```

查看執行資訊：

```powershell
Get-ScheduledTask -TaskName "HermesGateway" | Get-ScheduledTaskInfo
```

---

# 24. 手動啟動 Gateway Task

```powershell
Start-ScheduledTask -TaskName "HermesGateway"
```

確認：

```powershell
Get-ScheduledTask -TaskName "HermesGateway" | Get-ScheduledTaskInfo
```

---

# 25. 停止 Gateway

```powershell
Stop-ScheduledTask -TaskName "HermesGateway"
```

---

# 26. 移除 Gateway 自動啟動

優先使用：

```powershell
hermes gateway uninstall
```

如果該版本不提供此指令，可使用：

```powershell
Unregister-ScheduledTask -TaskName "HermesGateway" -Confirm:$false
```

---

# 27. Hermes Home

確認：

```powershell
$env:HERMES_HOME
```

Windows Native 預設資料位置：

```text
%LOCALAPPDATA%\hermes
```

例如：

```text
C:\Users\<USER>\AppData\Local\hermes
```

如果需要自訂：

```powershell
[Environment]::SetEnvironmentVariable(
    "HERMES_HOME",
    "$env:USERPROFILE\.hermes",
    "User"
)
```

設定後重新開 PowerShell。

---

# 28. Git Bash

確認：

```powershell
$env:HERMES_GIT_BASH_PATH
```

搜尋：

```powershell
Get-ChildItem "$env:LOCALAPPDATA\hermes\git" -Recurse -Filter bash.exe -ErrorAction SilentlyContinue
```

官方 Installer 會協助設定 Git Bash 路徑。

---

# 29. UTF-8

確認：

```powershell
chcp
```

需要時：

```powershell
chcp 65001
```

如果有特殊相容性問題，可以設定：

```powershell
$env:HERMES_DISABLE_WINDOWS_UTF8="1"
```

一般情況不建議設定。

---

# 30. Hermes Doctor

最重要的維護指令：

```powershell
hermes doctor
```

遇到：

- 安裝問題
- Browser 問題
- Tool 問題
- Gateway 問題
- Node / Python 問題

建議先執行：

```powershell
hermes doctor
```

---

# 31. Hermes Model

查看 / 修改模型：

```powershell
hermes model
```

完整設定：

```powershell
hermes setup
```

---

# 32. Hermes Tools

查看 Tool：

```powershell
hermes tools
```

可用於檢查：

- Shell
- Browser
- Web
- File
- MCP
- 其他 Tools

---

# 33. Hermes Config

查看設定：

```powershell
hermes config
```

如果版本支援：

```powershell
hermes config set
```

可以修改個別設定。

---

# 34. 更新 Hermes

先進入 Agent 目錄：

```powershell
Set-Location "$env:LOCALAPPDATA\hermes\hermes-agent"
```

查看 Git：

```powershell
git status
```

更新後建議：

```powershell
hermes doctor
```

---

# 35. 重新安裝

如果 Hermes 損壞：

```powershell
hermes uninstall
```

重新安裝：

```powershell
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

---

# 36. 完全移除

⚠️ 這會刪除 Hermes 使用者資料。

先：

```powershell
hermes uninstall
```

再：

```powershell
Remove-Item -Recurse -Force "$env:LOCALAPPDATA\hermes"
```

如果曾使用 WSL Hermes，也可能存在：

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.hermes"
```

刪除前請確認不需要：

- 設定
- API Keys
- Sessions
- Skills
- Logs

---

# 37. 完整驗收 Checklist

| 項目       | 指令                     |
| ---------- | ------------------------ |
| Windows    | `Get-ComputerInfo`       |
| Hermes     | `hermes --version`       |
| PATH       | `Get-Command hermes`     |
| Doctor     | `hermes doctor`          |
| Python     | `python --version`       |
| uv         | `uv --version`           |
| Node       | `node --version`         |
| npm        | `npm --version`          |
| Git        | `git --version`          |
| Model      | `hermes model`           |
| Tools      | `hermes tools`           |
| Config     | `hermes config`          |
| Agent      | `hermes`                 |
| Gateway    | `hermes gateway`         |
| Auto Start | `hermes gateway install` |

---

# 38. 建議正式目錄

```text
C:\
└── Users\
    └── <USER>\
        └── AppData\
            └── Local\
                └── hermes\
                    │
                    ├── hermes-agent\
                    │   ├── venv\
                    │   └── source
                    │
                    ├── git\
                    ├── node\
                    ├── bin\
                    ├── skills\
                    ├── sessions\
                    ├── logs\
                    ├── config.yaml
                    └── .env
```

---

# 39. 接本機 LLM

如果要把 Hermes 與本機 GPU / LLM 整合，可以規劃：

```text
Windows
│
├── Hermes Agent
│
├── Ollama / vLLM / llama-server
│
├── RTX 5090
│
└── Local LLM
```

也可以做 Cloud / Local fallback：

```text
Hermes
   │
   ├── Cloud LLM
   │
   └── Local LLM
          │
          └── RTX 5090
```

Hermes Windows Native 文件列有 Local Ollama、LM Studio、llama-server 等本機模型路徑。

---

# 40. 最推薦的實際安裝順序

不要一次全部設定，依以下順序進行：

```text
① Windows
     ↓
② Hermes Installer
     ↓
③ hermes doctor
     ↓
④ Nous Portal / LLM
     ↓
⑤ hermes
     ↓
⑥ Shell / File
     ↓
⑦ Git
     ↓
⑧ Browser
     ↓
⑨ MCP
     ↓
⑩ Telegram / Discord
     ↓
⑪ Gateway
     ↓
⑫ Windows Scheduled Task
     ↓
⑬ Local LLM / Ollama
```

---

# 41. 第一階段最小安裝指令

真正開始安裝時，可以先只執行：

```powershell
# 1. 安裝 Hermes
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

關閉 PowerShell，再重新開啟。

```powershell
# 2. 確認 Hermes
Get-Command hermes

# 3. 查看版本
hermes --version

# 4. 系統檢查
hermes doctor

# 5. 設定 Nous Portal
hermes setup --portal

# 6. 啟動 Hermes
hermes

# 7. 查看 Tools
hermes tools
```

---

## 官方參考

Hermes Agent 官方 GitHub：

https://github.com/NousResearch/hermes-agent

Windows Native 文件：

https://github.com/NousResearch/hermes-agent/blob/main/website/docs/user-guide/windows-native.md

Installation 文件：

https://github.com/NousResearch/hermes-agent/blob/main/website/docs/getting-started/installation.md

---

## 注意事項

1. API Key、Bot Token 等敏感資訊不要提交到 Git。
2. `.env` 建議只保存在 Hermes 使用者目錄。
3. 第一次安裝完成後先執行 `hermes doctor`。
4. Gateway 正式常駐前，先手動執行 `hermes gateway` 測試。
5. 如果需要完整 POSIX/Linux 環境，可考慮 WSL2。
6. 如果目標是 RTX 5090 本機 AI Agent，建議先完成 Hermes + Cloud LLM，再加入 Ollama / vLLM，方便排錯。
