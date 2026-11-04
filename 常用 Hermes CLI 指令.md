# 使用 Ollama 模型

```sh
ollama launch hermes
```

# 常用 Hermes CLI 指令

| 指令                         | 功能                   |
| ---------------------------- | ---------------------- |
| hermes                       | 啟動互動式 AI          |
| hermes --version             | 查看版本               |
| hermes --help                | 查看指令說明           |
| hermes setup                 | 初始設定               |
| hermes setup --portal Portal | 快速設定               |
| hermes chat                  | 啟動對話               |
| hermes model                 | 設定 AI 模型           |
| hermes auth                  | 管理 API / OAuth 認證  |
| hermes config                | 管理設定檔             |
| hermes update                | 更新 Hermes            |
| hermes logs                  | 查看日誌               |
| hermes skills list           | 查看 Skills            |
| hermes gateway status        | 查看 Gateway           |
| hermes sessions              | 管理對話紀錄           |
| hermes profile list          | 查看不同 Agent Profile |
| hermes uninstall             | 移除 Hermes            |

# AI 模型操作

```sh
# 進入模型設定
hermes model

# 指定模型啟動
hermes chat --model "模型名稱"

# 指定 Provider
hermes chat --provider ollama

# 單次提問
hermes chat -q "請幫我分析這個 Python 專案"

# 指定工具
hermes chat --toolsets "web,terminal,skills"

# 恢復最近一次對話
hermes --continue
```

# Skills / Agent 管理

```sh
# 查看 Skills
hermes skills list

# 搜尋 Skills
hermes skills search

# 安裝 Skill
hermes skills install

# 查看 Profile
hermes profile list

# 建立 Profile
hermes profile create dev

# 切換 Profile
hermes profile use dev
```

# Hermes 對話內 Slash Commands

注意：這些指令是在 Hermes 對話介面內輸入，不是在 PowerShell 輸入。

| Slash Command             | 功能                             |
| ------------------------- | -------------------------------- |
| /help                     | 說明                             |
| /model                    | 切換模型                         |
| /model custom:qwen3.5-64k | 切換本機模型                     |
| /model custom --global    | 將 Custom 設為預設（依目前設定） |
| /tools                    | 工具管理                         |
| /skills                   | Skills 管理                      |
| /memory                   | 記憶管理                         |
| /status                   | 狀態                             |
| /usage                    | 用量                             |
| /resume                   | 恢復對話                         |
| /new                      | 新對話                           |
| /quit                     | 離開                             |
| /exit                     | 離開                             |

---

# Hermes 主要 CLI 指令

1. 基本啟動與工作階段

   | 指令                       | 說明                  |
   | -------------------------- | --------------------- |
   | hermes                     | 啟動互動式 Agent      |
   | hermes chat                | 啟動對話              |
   | hermes chat -q             | "問題" 輸入問題       |
   | hermes chat --oneshot -q   | "問題" 單次提問後結束 |
   | hermes --version           | 查看版本              |
   | hermes --help              | 查看全部指令          |
   | hermes chat --help         | 查看對話參數          |
   | hermes --continue          | 繼續最近對話          |
   | hermes --resume latest     | 恢復最近工作階段      |
   | hermes --resume SESSION_ID | 恢復指定工作階段      |
   | hermes --in D:\Project     | 指定工作目錄          |
   | hermes --worktree          | 使用隔離 Git Worktree |
   | hermes --tui               | 啟動終端 UI           |
   | hermes --cli               | 使用傳統 CLI          |

2. 模型與 Provider

   | 指令                | 說明                   |
   | ------------------- | ---------------------- |
   | hermes model        | 模型設定選單           |
   | hermes moa          | Mixture of Agents 設定 |
   | hermes fallback     | 模型故障備援設定       |
   | hermes auth         | 認證管理               |
   | hermes auth list    | 列出認證               |
   | hermes auth status  | 查看認證狀態           |
   | hermes auth logout  | 登出 Provider          |
   | hermes usage        | 查看用量               |
   | hermes usage --json | JSON 格式用量          |

3. 設定檔管理

   | 指令                                         | 說明               |
   | -------------------------------------------- | ------------------ |
   | hermes config show                           | 顯示設定           |
   | hermes config edit                           | 編輯設定檔         |
   | hermes config get model.default              | 查詢預設模型       |
   | hermes config get model.provider             | 查詢 Provider      |
   | hermes config set model.default qwen3.5-64k  | 修改模型           |
   | hermes config set model.context_length 65536 | 設定 Context       |
   | hermes config path                           | 查看設定檔路徑     |
   | hermes config env-path                       | 查看環境變數檔路徑 |
   | hermes config check                          | 檢查設定           |
   | hermes config migrate                        | 遷移設定           |

4. Skills 管理

   | 指令                         | 說明              |
   | ---------------------------- | ----------------- |
   | hermes skills                | Skills 管理       |
   | hermes skills list           | 列出已安裝 Skills |
   | hermes skills browse         | 瀏覽 Skills       |
   | hermes skills search react   | 搜尋 React Skill  |
   | hermes skills inspect NAME   | 檢視 Skill        |
   | hermes skills install NAME   | 安裝 Skill        |
   | hermes skills uninstall NAME | 移除 Skill        |
   | hermes skills update         | 更新 Skills       |
   | hermes skills check          | 檢查更新          |
   | hermes skills audit          | 安全檢查          |
   | hermes skills config         | Skills 設定       |

5. Gateway 與通訊

   | 指令                     | 說明             |
   | ------------------------ | ---------------- |
   | hermes gateway setup     | 設定通訊平台     |
   | hermes gateway run       | 前景執行 Gateway |
   | hermes gateway start     | 啟動服務         |
   | hermes gateway stop      | 停止服務         |
   | hermes gateway restart   | 重啟服務         |
   | hermes gateway status    | 查看狀態         |
   | hermes gateway list      | 列出 Gateway     |
   | hermes gateway install   | 安裝背景服務     |
   | hermes gateway uninstall | 移除背景服務     |
   | hermes send              | 發送訊息         |
   | hermes pairing list      | 查看配對使用者   |

6. MCP 管理

   | 指令                          | 說明            |
   | ----------------------------- | --------------- |
   | hermes mcp                    | 開啟 MCP 選單   |
   | hermes mcp catalog            | 列出 MCP 目錄   |
   | hermes mcp list               | 列出 MCP Server |
   | hermes mcp install NAME       | 安裝 MCP        |
   | hermes mcp add NAME --url URL | 新增 MCP        |
   | hermes mcp remove NAME        | 移除 MCP        |
   | hermes mcp test NAME          | 測試 MCP        |
   | hermes mcp login NAME         | 重新認證        |

7. Profile / Session 管理

   | 指令                       | 說明          |
   | -------------------------- | ------------- |
   | hermes profile             | Profile 管理  |
   | hermes profile list        | 列出 Profiles |
   | hermes profile create NAME | 建立 Profile  |
   | hermes profile use NAME    | 切換 Profile  |
   | hermes sessions            | 工作階段管理  |
   | hermes sessions export     | 匯出對話      |
   | hermes sessions prune      | 清理對話      |
   | hermes sessions rename     | 重新命名對話  |
   | hermes sessions delete     | 刪除對話      |

8. 診斷、更新及維護

   | 指令                  | 說明             |
   | --------------------- | ---------------- |
   | hermes status         | 查看 Agent 狀態  |
   | hermes doctor         | 系統診斷         |
   | hermes doctor --fix   | 嘗試自動修復     |
   | hermes logs           | 查看日誌         |
   | hermes dump           | 匯出診斷資訊     |
   | hermes prompt-size    | 查看 Prompt 大小 |
   | hermes insights       | 查看使用分析     |
   | hermes backup         | 備份             |
   | hermes import         | 還原備份         |
   | hermes update         | 更新 Hermes      |
   | hermes update --check | 檢查更新         |
   | hermes uninstall      | 解除安裝         |
