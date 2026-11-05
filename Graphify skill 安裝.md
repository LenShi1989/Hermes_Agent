1. 安裝 Graphify CLI

建議使用 uv 安裝，避免 Python 套件環境衝突。

先開啟 PowerShell：

```sh
uv --version
```

如果尚未安裝 uv：

```sh
winget install --id astral-sh.uv -e
```

安裝 Graphify：
如果尚未安裝 uv：

```sh
uv tool install graphifyy
```

注意：PyPI 套件名稱是 graphifyy（兩個 y），但執行指令是 graphify。

確認安裝：

```sh
graphify --version
```

2. 安裝 Graphify Skill 到 Hermes

執行：

```sh
graphify hermes install
```

這是 Graphify 官方提供的 Hermes 專用安裝指令。

官方也支援：

```sh
graphify install --platform hermes
```

兩者都是安裝到 Hermes 的方式。

3. 確認 Skill 安裝位置

Windows 版本 Hermes 使用的全域 Skill 路徑是：

```sh
%LOCALAPPDATA%\hermes\skills\graphify\
```

通常完整路徑：

```sh
C:\Users\你的使用者名稱\AppData\Local\hermes\skills\graphify\
```

PowerShell 檢查：

```sh
Test-Path "$env:LOCALAPPDATA\hermes\skills\graphify\SKILL.md"
```

如果回傳：

```sh
True
```

代表 Skill 檔案存在。

查看內容：

```sh
Get-Content "$env:LOCALAPPDATA\hermes\skills\graphify\SKILL.md"
```

4. 重新啟動 Hermes

關閉目前 Hermes CLI，再重新執行：

```sh
hermes
```

接著在 Hermes 對話中測試：

```sh
/graphify
```

也可以直接要求：
請使用 Graphify 分析目前專案，建立程式碼知識圖譜，
分析模組、函式、類別、相依關係及主要執行流程。

5. 安裝到指定專案（選用）

如果不希望所有 Hermes 工作區都使用，可以採用專案級安裝。

先進入專案目錄：

```sh
cd "C:\Users\Len\Documents\1.GitHub\benton_system"
```

執行：

```sh
graphify hermes install --project
```

安裝位置：

```sh
你的專案\
└── .hermes\
    └── skills\
        └── graphify\
            └── SKILL.md
```

6. 常用管理指令

| 功能              | 指令                              |
| ----------------- | --------------------------------- |
| 安裝 Graphify     | uv tool install graphifyy         |
| 更新 Graphify     | uv tool upgrade graphifyy         |
| 安裝 Hermes Skill | graphify hermes install           |
| 專案級安裝        | graphify hermes install --project |
| 移除 Hermes Skill | graphify hermes uninstall         |
| 查看版本          | graphify --version                |
