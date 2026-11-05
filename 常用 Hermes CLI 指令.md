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

# Hermes Agent 對話內斜線指令中文翻譯

1. Session（工作階段管理）

| 指令          | 中文功能                                  |
| ------------- | ----------------------------------------- |
| /new [name]   | 建立新的工作階段                          |
| /reset        | 重設工作階段（等同 /new）                 |
| /clear        | 清除畫面並建立新工作階段                  |
| /redraw       | 強制重新繪製終端機畫面                    |
| /history      | 查看對話歷史                              |
| /save         | 匯出對話紀錄                              |
| /retry        | 重新執行上一個請求                        |
| /prompt       | 使用文字編輯器撰寫提示詞                  |
| /compose      | 同 /prompt                                |
| /undo [N]     | 回退指定數量的使用者對話                  |
| /title [name] | 設定工作階段名稱                          |
| /handoff      | 將工作階段轉交給 Telegram、Discord 等平台 |
| /branch       | 建立對話分支                              |
| /fork         | 同 /branch                                |
| /worktree     | 管理 Git 工作樹                           |
| /rollback     | 查看或還原檔案檢查點                      |
| /snapshot     | 建立或還原 Hermes 設定快照                |
| /snap         | 同 /snapshot                              |
| /stop         | 停止所有背景程序                          |
| /review       | 啟動獨立 Agent 審查目前工作               |
| /plan         | 建立 Markdown 開發計畫，不執行程式        |
| /egress       | 查看 Docker 網路出口代理狀態              |
| /resume       | 恢復指定名稱的工作階段                    |
| /sessions     | 瀏覽及恢復歷史工作階段                    |

2. Context（上下文管理）

   | 指令      | 中文功能                               |
   | --------- | -------------------------------------- |
   | /compress | 壓縮對話上下文                         |
   | /compact  | 同 /compress                           |
   | /status   | 查看 Session、模型、Token 及上下文狀態 |
   | /context  | 查看詳細上下文使用量                   |
   | /ctx      | 同 /context                            |

範例：

```sh
/compress here 10
```

保留最近 10 輪對話，壓縮較早的上下文。

```sh
/context all
```

3. Background & Automation（背景任務與自動化）

| 指令          | 中文功能                                |
| ------------- | --------------------------------------- |
| /bg           | 在獨立背景 Session 執行任務             |
| /btw          | 詢問旁支問題，不中斷目前任務            |
| /agents       | 查看執行中的 Agent                      |
| /tasks        | 同 /agents                              |
| /journey      | 查看學習歷程                            |
| /learning     | 同 /journey                             |
| /memory-graph | 開啟學習歷程時間軸                      |
| /queue        | 將提示詞加入執行佇列                    |
| /q            | 同 /queue                               |
| /steer        | 在下一次工具呼叫後注入指示              |
| /s            | 同 /steer                               |
| /goal         | 設定持續執行的目標                      |
| /heartbeat    | 設定定期觸發的提示詞                    |
| /hb           | 同 /heartbeat                           |
| /refine       | 從對話整理經驗並儲存至記憶或 Skills     |
| /loop         | 按指定間隔重複執行任務                  |
| /proactive    | 同 /loop                                |
| /moa          | 使用預設 Mixture of Agents 模式執行任務 |
| /subgoal      | 新增或管理子目標                        |

範例：

```sh
/bg 分析目前專案的所有 Python 程式碼
```

```sh
/btw 這個函式有沒有更有效率的寫法？
```

```sh
/queue 完成目前任務後，接著執行單元測試
```

```sh
/goal 完成 WT32-ETH01 Gateway 專案
```

4. Configuration（設定管理）

   | 指令           | 中文功能                             |
   | -------------- | ------------------------------------ |
   | /export        | 匯出設定檔、Skills 及主題設定        |
   | /import        | 匯入其他 Hermes 設定檔               |
   | /config        | 查看目前設定                         |
   | /model         | 切換 AI 模型                         |
   | /codex-runtime | 切換 Codex App Server 執行模式       |
   | /codex_runtime | 同上                                 |
   | /personality   | 切換 Agent 人格                      |
   | /statusbar     | 開關狀態列                           |
   | /sb            | 同 /statusbar                        |
   | /battery       | 開關電量指示器                       |
   | /timestamps    | 開關訊息時間戳記                     |
   | /ts            | 同 /timestamps                       |
   | /verbose       | 切換工具執行過程顯示程度             |
   | /focus         | 切換專注模式，只顯示提示詞與最終回答 |
   | /footer        | 顯示或隱藏執行環境資訊               |
   | /yolo          | 切換 YOLO 模式（略過危險指令確認）   |
   | /approvals     | 設定危險指令核准模式                 |
   | /reasoning     | 管理推理強度與顯示方式               |
   | /skin          | 更換終端機外觀主題                   |
   | /indicator     | 更換執行中動畫樣式                   |
   | /voice         | 開啟或關閉語音模式                   |
   | /wake          | 開啟或關閉 Hey Hermes 喚醒詞         |
   | /busy          | 設定忙碌期間訊息處理方式             |
   | /autoprune     | 開啟或關閉舊 Session 自動清理        |

模型切換範例

```sh
/model
```

```sh
/model qwen3.5:9b
```

```sh
/model qwen3.5:9b --provider ollama
```

```sh
/model reset
```

注意：實際模型名稱與 Provider 必須符合你的 Hermes 設定。

5. Info（資訊查詢）

| 指令          | 中文功能                    |
| ------------- | --------------------------- |
| /whoami       | 查看目前使用者權限          |
| /profile      | 查看目前 Profile 名稱及目錄 |
| /diff         | 查看 Git 修改內容           |
| /help         | 查看指令說明                |
| /palette      | 開啟模糊搜尋指令面板        |
| /usage        | 查看 Token 用量及速率限制   |
| /subscription | 查看 Nous 訂閱方案          |
| /upgrade      | 同 /subscription            |
| /login        | 登入 Nous 帳戶              |
| /topup        | 查看餘額及管理付款          |
| /insights     | 查看使用統計                |
| /platforms    | 查看 Gateway 及訊息平台狀態 |
| /gateway      | 同 /platforms               |
| /copy         | 複製最近的 AI 回答          |
| /paste        | 貼上剪貼簿圖片              |
| /image        | 附加本機圖片                |
| /update       | 更新 Hermes Agent           |
| /version      | 查看 Hermes 版本            |
| /v            | 同 /version                 |
| /debug        | 上傳除錯報告並取得分享連結  |

6. Tools & Skills（工具與技能管理）

   | 指令           | 中文功能                               |
   | -------------- | -------------------------------------- |
   | /tools         | 管理工具啟用或停用                     |
   | /toolsets      | 列出可用工具組                         |
   | /skills        | 搜尋、安裝、查看及管理 Skills          |
   | /memory        | 查看待寫入記憶及管理核准設定           |
   | /bundles       | 列出技能組合                           |
   | /pet           | 管理 Hermes 寵物角色                   |
   | /hatch         | 根據描述產生新寵物                     |
   | /generate-pet  | 同 /hatch                              |
   | /learn         | 從指定內容學習並建立可重複使用的 Skill |
   | /init          | 掃描專案並建立或更新 AGENTS.md         |
   | /cron          | 管理排程任務                           |
   | /suggestions   | 管理建議的自動化任務                   |
   | /suggest       | 同 /suggestions                        |
   | /blueprint     | 使用範本建立自動化                     |
   | /bp            | 同 /blueprint                          |
   | /curator       | 背景維護 Skills                        |
   | /kanban        | 管理多 Profile 協作看板                |
   | /reload        | 重新載入 .env 環境變數                 |
   | /reload-mcp    | 重新載入 MCP 伺服器                    |
   | /reload_mcp    | 同 /reload-mcp                         |
   | /reload-skills | 重新掃描 Skills 目錄                   |
   | /reload_skills | 同 /reload-skills                      |
   | /browser       | 連接目前 Chromium 瀏覽器               |
   | /plugins       | 查看已安裝 Plugins 及其狀態            |

7. Exit（離開）
   | 指令 | 中文功能 |
   |-------|---------------|
   | /quit | 離開 Hermes CLI |
   | /exit | 同 /quit |

```sh
/quit
```

刪除 Session 歷史後離開：

```sh
/quit --delete
```

8. 針對你的本機 Ollama 開發環境，推薦指令

   以下是我建議優先熟悉的組合：

   | 使用情境                     | 指令           |
   | ---------------------------- | -------------- |
   | 切換 Qwen 模型               | /model         |
   | 確認目前模型                 | /status        |
   | 查看 GPU／Token 相關執行資訊 | /status        |
   | 查看上下文佔用               | /context       |
   | 壓縮記憶                     | /compress      |
   | 分析專案                     | /init          |
   | 規劃開發流程                 | /plan          |
   | 程式碼審查                   | /review        |
   | 查看修改紀錄                 | /diff          |
   | 還原程式                     | /rollback      |
   | 建立開發技能                 | /learn         |
   | 重新載入技能                 | /reload-skills |
   | 管理背景 Agent               | /agents        |
   | 停止所有背景程序             | /stop          |

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

# 指定模型 context

建議改用 Modelfile 固定指定 64K
請直接在 PowerShell 依序執行。

1. 建立專屬模型設定

```sh
@'
FROM qwen2.5-coder:7b
PARAMETER num_ctx 65536
'@ | Set-Content -Encoding ascii .\Modelfile-qwen64k
```

2. 建立新模型

```sh
ollama create qwen2.5-coder:7b-64k -f .\Modelfile-qwen64k
```

3. 確認模型設定

```sh
ollama show qwen2.5-coder:7b-64k --modelfile
```

應該看到：

```sh
PARAMETER num_ctx 65536
```

4. 啟動新模型

```sh
ollama run qwen2.5-coder:7b-64k
```

輸入：

```txt
你好
```

再開另一個 PowerShell：

```sh
ollama ps
```

預期：

```sh
NAME                       CONTEXT
qwen2.5-coder:7b-64k       65536
```

重要：Hermes 也要同步修改
等 Ollama 確認 64K 後，再將 Hermes 的模型名稱改為：

```sh
qwen2.5-coder:7b-64k
```
