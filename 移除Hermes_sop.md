## 1. 先確認 Hermes 安裝位置

PowerShell 執行：

```sh
Get-Command hermes -All
```

再執行：

```sh
where.exe hermes
```

如果看到類似：

```sh
C:\Users\Len\AppData\Local\hermes\bin\hermes.exe
```

代表就是你之前這個安裝。

---

## 2. 先關閉 Hermes Agent

如果目前有 Hermes 在執行：

```sh
Get-Process | Where-Object {
    $_.ProcessName -match "hermes"
}
```

如果有程序，可以：

```sh
Get-Process | Where-Object {
    $_.ProcessName -match "hermes"
} | Stop-Process -Force
```

---

## 3. 移除 Hermes Agent 主目錄

你之前的目錄是：

```sh
C:\Users\Len\AppData\Local\hermes
```

先確認：

```sh
Test-Path "$env:LOCALAPPDATA\hermes"
```

如果回傳：

```sh
True
```

刪除：

```sh
Remove-Item "$env:LOCALAPPDATA\hermes" -Recurse -Force
```

---

## 4. 檢查 PATH 是否還有 Hermes

查看目前 User PATH：

```sh
[Environment]::GetEnvironmentVariable("Path", "User") -split ";"
```

找：

```sh
C:\Users\Len\AppData\Local\hermes\bin
```

如果存在，需要移除。

PowerShell 自動移除

```sh
$userPath = [Environment]::GetEnvironmentVariable("Path", "User")

$newPath = ($userPath -split ";" | Where-Object {
    $_ -and ($_ -notlike "*\AppData\Local\hermes*")
}) -join ";"

[Environment]::SetEnvironmentVariable("Path", $newPath, "User")
```

---

## 5. 關閉目前 PowerShell

這一步很重要。

PATH 已經修改，但目前 PowerShell Session 可能還保留舊 PATH。

直接關閉 PowerShell。

然後重新開一個 PowerShell 7。

---

## 6. 驗證 Hermes 已經移除

重新開 PowerShell：

```sh
Get-Command hermes -ErrorAction SilentlyContinue
```

以及：

```sh
where.exe hermes
```

正常應該沒有任何輸出。

也可以：

```sh
Test-Path "$env:LOCALAPPDATA\hermes"
```

應該得到：

```sh
False
```

---

## 7. 檢查是否有 Hermes 設定殘留

因為你之前有看到：

```sh
Deprecated .env settings detected
TERMINAL_CWD=...
```

所以建議搜尋使用者目錄。

```sh
Get-ChildItem "$env:USERPROFILE" -Force -Recurse -ErrorAction SilentlyContinue |
    Where-Object {
        $_.Name -match "hermes"
    } |
    Select-Object FullName
```

如果找到例如：

```sh
C:\Users\Len\.hermes
C:\Users\Len\.config\hermes
```

再確認內容後刪除。

例如：

```sh
Remove-Item "$env:USERPROFILE\.hermes" -Recurse -Force
```

如果有：

```sh
C:\Users\Len\.config\hermes
```

則：

```sh
Remove-Item "$env:USERPROFILE\.config\hermes" -Recurse -Force
```

不要直接刪除整個 .config。

---

## 8. 檢查 Python / pip 是否安裝 Hermes

如果你之前是透過 Python 安裝，也檢查：

```sh
pip list | Select-String -Pattern "hermes"
```

以及：

```sh
python -m pip list | Select-String -Pattern "hermes"
```

如果看到 Hermes 相關套件，再執行：

```sh
python -m pip uninstall hermes
```

如果實際套件名稱不同，不要直接猜，先把 `pip list` 結果貼給我。

---

## 9. 檢查 npm 是否有 Hermes

如果之前曾經透過 npm：

```sh
npm list -g --depth=0 | Select-String -Pattern "hermes"
```

如果找到 Hermes 套件，再移除：

```sh
npm uninstall -g <套件名稱>
```

---

# 最後完整驗證

重新開 PowerShell 後執行：

```sh
Write-Host "=== Hermes Command ==="
Get-Command hermes -ErrorAction SilentlyContinue

Write-Host "`n=== Hermes PATH ==="
where.exe hermes 2>$null

Write-Host "`n=== Hermes Directory ==="
Test-Path "$env:LOCALAPPDATA\hermes"

Write-Host "`n=== Hermes Processes ==="
Get-Process | Where-Object {
    $_.ProcessName -match "hermes"
}
```

理想結果：

```sh
=== Hermes Command ===

=== Hermes PATH ===

=== Hermes Directory ===
False

=== Hermes Processes ===
```

這樣就代表 Windows 上的 Hermes Agent 基本上已完整移除。
