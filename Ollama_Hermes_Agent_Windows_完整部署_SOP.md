# Windows 11 + Ollama + Hermes Agent 完整部署 SOP

> 版本：2026-08-16  
> 目標：在 Windows 11 建立本機 AI Coding Agent 環境，以 Ollama 提供本地 LLM，Hermes Agent 提供 Agent、Terminal、Git、檔案操作與 Coding 能力。  
> 適用情境：希望建立類似 Claude Code / Codex / OpenCode 的本地 Coding Agent。

---

## 1. 最終架構

```text
Windows 11
│
├── PowerShell 7
├── Git
├── VS Code
│
├── Ollama
│   ├── Local LLM
│   ├── OpenAI-compatible API
│   └── http://localhost:11434/v1
│
└── Hermes Agent
    ├── Terminal Tool
    ├── File Tool
    ├── Git
    ├── Browser / Web Tools
    ├── Memory
    ├── Skills
    └── Coding Agent
             │
             ▼
          Ollama
             │
             ▼
         Local LLM
```

核心流程：

```text
User
 ↓
Hermes Agent
 ↓
Tool Calling
 ↓
Ollama
 ↓
Local LLM
 ↓
Terminal / File / Git / Python / Node / Docker
```

---

# 2. 系統需求

## 2.1 建議環境

| 項目       | 建議                              |
| ---------- | --------------------------------- |
| OS         | Windows 11 64-bit                 |
| PowerShell | PowerShell 7.x                    |
| RAM        | 32 GB 以上                        |
| GPU        | NVIDIA / AMD GPU 佳               |
| VRAM       | 16 GB 以上較適合大型 Coding Model |
| SSD        | 至少 100 GB 可用空間              |
| Git        | 最新版本                          |
| Python     | Hermes Installer 會處理相依版本   |
| Node.js    | Hermes Installer 會處理相依版本   |
| Docker     | Docker Desktop，可選              |

> 模型越大，需要的 RAM / VRAM 越高。31B 等級模型不代表所有 Windows 電腦都能順暢執行。

---

# 3. 安裝前檢查

以 PowerShell 7 執行：

```powershell
$PSVersionTable.PSVersion
```

確認 Windows：

```powershell
winver
```

確認 Git：

```powershell
git --version
```

確認 GPU：

```powershell
Get-CimInstance Win32_VideoController |
    Select-Object Name, AdapterRAM
```

NVIDIA 使用者可另外確認：

```powershell
nvidia-smi
```

確認磁碟：

```powershell
Get-PSDrive C,D
```

建議至少保留：

```text
C: 20 GB+
D: 100 GB+
```

如果沒有 D 槽，可以全部使用 C 槽。

---

# 4. 安裝 PowerShell 7

安裝 PowerShell 7

```sh
winget install --id Microsoft.PowerShell -e
```

確認：

```sh
pwsh --version
```

建議使用 PowerShell 7.x。

之後所有安裝指令，優先使用：

```sh
PowerShell 7
```

不要混用舊版 Windows PowerShell 5.1。

---

# 5. 安裝 Git

如果尚未安裝 Git，可使用：

```powershell
winget install --id Git.Git -e
```

安裝完成後重新開 PowerShell：

```powershell
git --version
```

設定基本資訊：

```powershell
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

確認：

```powershell
git config --global --list
```

---

# 6. 安裝 Ollama

## 6.1 官方 PowerShell 安裝方式

```powershell
irm https://ollama.com/install.ps1 | iex
```

安裝完成後重新開 PowerShell。

確認：

```powershell
ollama --version
```

---

# 7. 啟動 Ollama

Windows 版通常會在背景執行。

確認 API：

```powershell
curl http://localhost:11434/api/tags
```

或：

```powershell
Invoke-RestMethod http://localhost:11434/api/tags
```

正常情況會回傳 JSON。

例如：

```json
{
  "models": []
}
```

代表：

```text
Ollama Server OK
```

---

# 8. 設定 Ollama 模型目錄

如果 C 槽空間有限，建議將模型放到 D 槽。

建立：

```powershell
New-Item -ItemType Directory -Force D:\Ollama\Models
```

設定環境變數：

```powershell
[Environment]::SetEnvironmentVariable(
    "OLLAMA_MODELS",
    "D:\Ollama\Models",
    "User"
)
```

重新開 PowerShell。

確認：

```powershell
[Environment]::GetEnvironmentVariable(
    "OLLAMA_MODELS",
    "User"
)
```

應看到：

```text
D:\Ollama\Models
```

---

# 9. 設定 Ollama Context

Hermes Agent 屬於長上下文 Agent，建議至少：

```text
64K tokens
```

設定：

```powershell
[Environment]::SetEnvironmentVariable(
    "OLLAMA_CONTEXT_LENGTH",
    "65536",
    "User"
)
```

目前 PowerShell session 同步設定：

```powershell
$env:OLLAMA_CONTEXT_LENGTH="65536"
```

重新啟動 Ollama。

如果 Ollama 正在執行：

```powershell
taskkill /F /IM ollama.exe
```

然後：

```powershell
ollama serve
```

> 如果你是透過 Windows Ollama App 啟動 Ollama，也可以關閉後重新開啟 App。

---

# 10. 下載 LLM

## 10.1 第一階段測試

建議先使用你硬體能負擔的模型。

例如：

```powershell
ollama pull qwen2.5-coder:7b
```

或依實際硬體選擇較小模型。

確認：

```powershell
ollama list
```

例如：

```text
NAME                    SIZE
qwen2.5-coder:7b     ...
```

---

# 11. 測試 Ollama 模型

執行：

```powershell
ollama run qwen2.5-coder:7b
```

輸入：

```text
請用 Python 寫一個 Hello World。
```

如果正常回答：

```text
Ollama
  ↓
Model
  ↓
正常
```

離開：

```text
/bye
```

---

# 12. 確認 Ollama Context

執行：

```powershell
ollama ps
```

確認實際載入模型與 Context。

如果模型已載入，應可以看到模型資訊。

> 如果 Context 沒有達到預期，先檢查 `OLLAMA_CONTEXT_LENGTH`，再重新啟動 Ollama。

---

# 13. 安裝 Hermes Agent

使用 Hermes 官方 Windows Installer：

```powershell
iex (irm https://hermes-agent.nousresearch.com/install.ps1)
```

安裝程式會處理 Hermes 所需的部分環境，例如：

```text
Python
uv
Node.js
ripgrep
ffmpeg
Git Bash
Hermes Agent
```

安裝完成後重新開 PowerShell。

---

# 14. 確認 Hermes

```powershell
hermes --version
```

再執行：

```powershell
hermes doctor
```

建議確認：

```text
Python       OK
uv           OK
Node.js      OK
Git          OK
ffmpeg       OK
Tools        OK
Configuration OK
```

如果 `hermes` 找不到：

```powershell
Get-Command hermes
```

以及：

```powershell
$env:Path -split ";"
```

重新開 PowerShell 後通常即可解決 PATH 更新問題。

---

# 15. Hermes 初始設定

執行：

```powershell
hermes setup
```

依照互動式設定完成初始配置。

查看模型設定：

```powershell
hermes model
```

---

# 16. 設定 Ollama Provider

Hermes 使用 Ollama 時，設定：

```text
Provider:
Custom Endpoint

Base URL:
http://localhost:11434/v1

API Key:
留空

Model:
qwen2.5-coder:7b
```

Ollama 本身提供 OpenAI-compatible API。

---

# 17. Hermes config.yaml

如果需要手動設定，可以查看：

```powershell
notepad "$env:USERPROFILE\.hermes\config.yaml"
```

依 Hermes 版本實際產生的設定格式為準。

概念上需要：

```yaml
model:
  default: qwen2.5-coder:7b
  context_length: 65536
  ollama_num_ctx: 65536
  provider: custom
  base_url: http://localhost:11434/v1

agent:
  reasoning_effort: none
```

> 如果目前 Hermes 版本產生的 `config.yaml` 結構不同，優先使用 `hermes setup` / `hermes model`，不要直接覆蓋整份設定檔。

---

# 18. 測試 Hermes + Ollama

啟動：

```powershell
hermes
```

輸入：

```text
Hello，請確認你目前可以正常工作。
```

如果成功：

```text
Hermes
  ↓
Ollama API
  ↓
Local LLM
```

已打通。

---

# 19. 測試 Terminal Tool

在 Hermes：

```text
請執行 PowerShell 指令：
Get-Date
```

如果 Agent 能執行並回傳結果：

```text
Terminal Tool OK
```

---

# 20. 測試 File Tool

建立測試資料夾：

```powershell
mkdir C:\HermesTest
cd C:\HermesTest
```

設定執行專案目錄：

```sh
hermes config set terminal.cwd "C:\Users\Len\Desktop\HermesTest"
```

啟動：

```powershell
hermes
```

輸入：

```text
建立 hello.py。
內容：
print("Hello Hermes")
```

然後：

```text
執行 hello.py，並告訴我執行結果。
```

預期：

```text
hello.py
   ↓
Python
   ↓
Hello Hermes
```

---

# 21. 測試 Git

建立 Git Repository：

```powershell
cd C:\HermesTest

git init
```

在 Hermes：

```text
查看目前 Git repository 狀態，
建立一個 README.md，
然後執行 git status。
```

確認 Agent 可以：

```text
讀取檔案
建立檔案
執行 Git
```

---

# 22. 測試 Coding Agent

建立專案：

```powershell
mkdir C:\HermesProjects
cd C:\HermesProjects
```

啟動：

```powershell
hermes
```

輸入：

```text
請建立一個 ASP.NET Core Web API 專案。

需求：
1. .NET 10
2. EF Core
3. PostgreSQL
4. Docker Compose
5. Swagger
6. Todo CRUD
7. README.md
8. Dockerfile
9. docker-compose.yml

請先分析架構。
確認後再建立檔案。
```

Agent 應該能協助：

```text
建立專案
↓
建立 Controller
↓
建立 Entity
↓
建立 DbContext
↓
建立 Migration
↓
建立 Dockerfile
↓
建立 docker-compose
↓
建立 README
```

---

# 23. VS Code 整合

安裝 VS Code：

```powershell
winget install Microsoft.VisualStudioCode
```

進入專案：

```powershell
cd C:\HermesProjects
code .
```

建議 VS Code Terminal 使用：

```text
PowerShell 7
```

確認：

```powershell
$PSVersionTable.PSVersion
```

可以在 VS Code Terminal 直接：

```powershell
hermes
```

形成：

```text
VS Code
│
├── Editor
│
├── Git
│
├── Terminal
│
└── Hermes Agent
       │
       ▼
     Ollama
       │
       ▼
     Local LLM
```

---

# 24. Docker Desktop 整合

如果你的 Coding Project 使用 Docker：

```powershell
docker --version
```

確認：

```powershell
docker version
```

再：

```powershell
docker compose version
```

測試：

```powershell
docker run --rm hello-world
```

如果正常：

```text
Docker OK
```

Hermes 就可以透過 Terminal 協助：

```text
docker build
docker compose up
docker compose down
docker logs
docker ps
```

---

# 25. 建議的工作目錄

建議建立：

```text
D:\AI\
│
├── Ollama\
│   └── Models\
│
├── Hermes\
│
├── Projects\
│   ├── AGV\
│   ├── Vision\
│   ├── ASPNET\
│   ├── Vue\
│   └── Python\
│
└── Workspace\
```

建立：

```powershell
New-Item -ItemType Directory -Force D:\AI\Projects
New-Item -ItemType Directory -Force D:\AI\Workspace
```

---

# 26. 建議的 Hermes 專案使用方式

每個專案建立獨立 Git Repository：

```text
D:\AI\Projects\AGV
D:\AI\Projects\Vision
D:\AI\Projects\ASPNET
D:\AI\Projects\Vue
```

例如：

```powershell
cd D:\AI\Projects\AGV
git init
hermes
```

這樣 Agent 的工作範圍會比較清楚。

---

# 27. 建議 Agent Prompt

第一次讓 Hermes 接手專案，不要直接說：

```text
幫我把這個專案做好。
```

建議：

```text
你現在是這個專案的 Senior Software Engineer。

工作規則：

1. 先分析專案，不要立即修改。
2. 先讀 README。
3. 先讀 package / project configuration。
4. 確認目前架構。
5. 確認 Build 是否正常。
6. 確認 Test 是否正常。
7. 修改前先說明方案。
8. 優先修改現有程式，不要任意重寫。
9. 不要刪除使用者資料。
10. 不要執行危險指令。
11. 修改完成後執行 Build。
12. 可以執行 Test 時執行 Test。
13. 最後整理修改內容。
```

---

# 28. Coding Agent 建議工作流程

```text
需求
 ↓
分析
 ↓
讀取 Repository
 ↓
建立 Plan
 ↓
修改程式
 ↓
Build
 ↓
Test
 ↓
修正 Error
 ↓
再次 Build
 ↓
Git Diff
 ↓
完成
```

要求 Hermes：

```text
完成工作後請執行：

git status
git diff
build
test

最後告訴我：
1. 修改哪些檔案
2. 為什麼修改
3. Build 結果
4. Test 結果
5. 還有哪些風險
```

---

# 29. 常用維護指令

## Ollama

查看版本：

```powershell
ollama --version
```

查看模型：

```powershell
ollama list
```

查看執行中的模型：

```powershell
ollama ps
```

下載：

```powershell
ollama pull MODEL
```

刪除：

```powershell
ollama rm MODEL
```

測試：

```powershell
ollama run MODEL
```

---

## Hermes

版本：

```powershell
hermes --version
```

診斷：

```powershell
hermes doctor
```

模型：

```powershell
hermes model
```

啟動：

```powershell
hermes
```

---

# 30. 常見問題

## Q1：`ollama` 找不到

執行：

```powershell
Get-Command ollama
```

如果沒有：

1. 關閉 PowerShell
2. 重新開 PowerShell
3. 再執行：

```powershell
ollama --version
```

---

## Q2：`hermes` 找不到

執行：

```powershell
Get-Command hermes
```

重新開 PowerShell。

再：

```powershell
hermes --version
```

如果仍然找不到：

```powershell
hermes doctor
```

需要檢查 Hermes 安裝目錄是否加入 PATH。

---

## Q3：Hermes 連不到 Ollama

測試：

```powershell
curl http://localhost:11434/api/tags
```

如果失敗：

```powershell
taskkill /F /IM ollama.exe
```

重新啟動：

```powershell
ollama serve
```

再測：

```powershell
curl http://localhost:11434/api/tags
```

---

## Q4：模型速度太慢

優先檢查：

```powershell
ollama ps
```

確認模型是否使用 GPU。

NVIDIA：

```powershell
nvidia-smi
```

如果 VRAM 不足，考慮：

```text
較小模型
降低 Context
增加 RAM
使用更強 GPU
```

---

## Q5：Context 不足

確認：

```powershell
[Environment]::GetEnvironmentVariable(
    "OLLAMA_CONTEXT_LENGTH",
    "User"
)
```

應該是：

```text
65536
```

重新啟動 Ollama。

再：

```powershell
ollama ps
```

---

## Q6：Windows Defender / 防毒軟體阻擋

不要直接關閉防毒。

優先確認：

```text
Ollama
Hermes
Python
Git
Node.js
PowerShell
```

是否被安全軟體限制。

---

# 31. 安全設定

本機 Agent 具有：

```text
Terminal
File System
Git
Docker
Network
```

權限。

因此不要一開始直接讓 Agent 工作在：

```text
C:\
C:\Windows
C:\Program Files
```

建議：

```text
D:\AI\Projects
```

或：

```text
C:\Users\<User>\Documents\AIProjects
```

並且：

```text
一個 Project
一個 Git Repository
```

重要專案先：

```powershell
git status
git commit
```

再讓 Agent 大幅修改。

---

# 32. 生產環境建議

如果要長期使用：

```text
Windows
│
├── Ollama
│
├── Hermes Agent
│
├── VS Code
│
├── Git
│
├── Docker Desktop
│
└── Projects
```

建議：

```text
Git
 ↓
Feature Branch
 ↓
Hermes Agent
 ↓
Build
 ↓
Test
 ↓
Code Review
 ↓
Merge
```

不要讓 Agent 直接在 production repository 的 main branch 大量修改。

---

# 33. 最終驗證 Checklist

- [ ] Windows 11 正常
- [ ] PowerShell 7 正常
- [ ] Git 正常
- [ ] Ollama 已安裝
- [ ] Ollama API 正常
- [ ] Ollama Model 已下載
- [ ] Ollama Context >= 64K
- [ ] Hermes Agent 已安裝
- [ ] `hermes --version` 正常
- [ ] `hermes doctor` 通過
- [ ] Hermes 已設定 Ollama Endpoint
- [ ] Hermes 可以呼叫 LLM
- [ ] Terminal Tool 正常
- [ ] File Tool 正常
- [ ] Git Tool 正常
- [ ] Python 正常
- [ ] Node.js 正常
- [ ] Docker 正常（如果需要）
- [ ] VS Code 正常
- [ ] Coding Project 可以 Build
- [ ] Coding Project 可以 Test

---

# 34. 一鍵檢查腳本

建立：

```text
D:\AI\check-hermes.ps1
```

內容：

```powershell
Write-Host "==================================" -ForegroundColor Cyan
Write-Host " Ollama + Hermes Environment Check"
Write-Host "==================================" -ForegroundColor Cyan

Write-Host "`n[PowerShell]"
$PSVersionTable.PSVersion

Write-Host "`n[Git]"
git --version

Write-Host "`n[Ollama]"
ollama --version

Write-Host "`n[Ollama API]"
try {
    Invoke-RestMethod http://localhost:11434/api/tags |
        Select-Object -ExpandProperty models
}
catch {
    Write-Host "Ollama API ERROR" -ForegroundColor Red
}

Write-Host "`n[Ollama Models]"
ollama list

Write-Host "`n[Ollama Running]"
ollama ps

Write-Host "`n[Hermes]"
hermes --version

Write-Host "`n[Hermes Doctor]"
hermes doctor

Write-Host "`n[Docker]"
try {
    docker version --format '{{.Server.Version}}'
}
catch {
    Write-Host "Docker unavailable"
}

Write-Host "`n=================================="
Write-Host " Check Complete"
Write-Host "=================================="
```

執行：

```powershell
Set-ExecutionPolicy -Scope Process Bypass
D:\AI\check-hermes.ps1
```

---

# 35. 建議最終架構

如果目標是取代 Claude Code / Codex，建議最後形成：

```text
                         Windows 11
                              │
             ┌────────────────┼────────────────┐
             │                │                │
          VS Code          PowerShell        Git
             │                │                │
             └────────────────┼────────────────┘
                              │
                       Hermes Agent
                              │
             ┌────────────────┼────────────────┐
             │                │                │
          Terminal          Files             Git
             │                │                │
             └────────────────┼────────────────┘
                              │
                            Ollama
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
       Coding Model        Reasoning Model     General Model
          │
          ▼
   Local AI Coding Agent
```

---

# 36. 後續模型選擇方向

不要只固定一個模型。

建議測試：

```text
DeepSeek
Qwen
Gemma
GLM
Devstral
```

比較：

```text
Coding
Reasoning
Tool Calling
中文
長 Context
速度
VRAM 使用量
```

尤其你的工作包含：

```text
ASP.NET Core
Vue
Python
Docker
PostgreSQL
MQTT
AGV
Computer Vision
YOLO
ROS / ROS2
```

因此模型選擇應該以「Coding + Agent Tool Calling」為主要評估標準，而不是只比較一般聊天能力。

---

# 37. 官方文件

Ollama Windows：

https://docs.ollama.com/windows

Ollama：

https://ollama.com/

Hermes Agent：

https://hermes-agent.nousresearch.com/docs/

Hermes Agent GitHub：

https://github.com/NousResearch/hermes-agent

---

# 38. 完成條件

當以下指令全部正常：

```powershell
ollama --version
ollama list
ollama ps
curl http://localhost:11434/api/tags
hermes --version
hermes doctor
```

並且：

```powershell
hermes
```

可以成功：

```text
讀取檔案
建立檔案
執行 PowerShell
執行 Python
執行 Git
執行 Build
執行 Test
```

即可視為：

```text
Windows + Ollama + Hermes Agent
本機 Coding Agent 部署完成
```

---

## 下一階段建議

完成基礎環境後，再進行：

```text
Phase 1
Windows + Ollama + Hermes
        ↓
Phase 2
選擇最佳 Coding Model
        ↓
Phase 3
Hermes + VS Code
        ↓
Phase 4
Hermes + Docker
        ↓
Phase 5
Hermes + Git/GitHub/GitLab
        ↓
Phase 6
Hermes Memory / Skills
        ↓
Phase 7
建立自己的 AI Coding Agent
```

最終目標：

```text
Claude Code / Codex
        ↓
本地替代方案
        ↓
Hermes Agent
        +
Ollama
        +
高品質 Coding Model
        +
Tools / Skills / Memory
```

---

# debug

## 查誰占用 port 11434

```sh
netstat -ano | findstr :11434
```

你應該會看到類似：

```sh
TCP    127.0.0.1:11434    0.0.0.0:0    LISTENING    12345
```

最後面的：

```sh
12345
```

就是 PID。

---

## 查 PID 是誰

假設 PID 是 12345：

```sh
Get-Process -Id 12345
```

也可以直接：

```sh
tasklist | findstr 12345
```

---

## 特別檢查 Ollama 所使用 PID

```sh
Get-Process ollama* -ErrorAction SilentlyContinue
```

## 測試 Ollama 是否正常運作

所以先測：

```sh
Invoke-RestMethod http://localhost:11434/api/tags
```

或者：

```sh
curl http://localhost:11434/api/tags
```

如果回傳 JSON

例如：

```sh
{
  "models": []
}
```

或：

```sh
{
  "models": [
    {
      "name": "gemma4:31b"
    }
  ]
}
```

那就代表：

```
Ollama Server
     ↓
127.0.0.1:11434
     ↓
正常
```

不用再執行 ollama serve。

---

## 再確認 Ollama

執行：

```sh
ollama list
```

以及：

```sh
ollama ps
```

---

## Hermes Agent 的最低要求是 64000 tokens, context length ≥ 65536

| 模型                  |     Context | RTX 3060 12GB       |
| --------------------- | ----------: | ------------------- |
| `qwen2.5-coder:7b`    | 可達 128K\* | ✅ 推薦             |
| `qwen3:8b`            | 可達 128K\* | ✅                  |
| `deepseek-coder:6.7b` |         16K | ❌ Hermes 不接受    |
| 14B 級 Coding Model   |        64K+ | ⚠️ 可能需要 CPU/RAM |
| 30B+                  |        64K+ | ❌ 不適合 12GB VRAM |

直接修改 context_length:

```sh
hermes config set model.context_length 65536
hermes config set model.ollama_num_ctx 65536
```

---

## 查詢Hermes Agent 的實際安裝位置。

請在 PowerShell 執行：

```sh
Get-Command hermes -ErrorAction SilentlyContinue | Format-List *
```

以及：

```sh
where.exe hermes
```
