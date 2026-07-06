
## 🔍 問題根源

Windows 的 **WSL2**（以及 Hyper-V）啟動時會**動態保留通訊埠範圍**，若不慎將 Port 1433 納入保留範圍，將導致 SQL Server 無法綁定該 Port 而無法啟動。

---

## 🧭 排查過程

### 1. 確認 Hyper-V 狀態
```powershell
Get-WindowsOptionalFeature -Online | Where-Object { $_.FeatureName -like "*Hyper*" } | Select-Object FeatureName, State
```

### 2. 確認 Docker 後端
```powershell
wsl --status
wsl -l -v
```

確認 Docker Desktop 使用 **WSL2 後端**，完全不依賴 Hyper-V，以下是正確結果
```shell
PS C:\WINDOWS\system32> wsl --status
預設通訊群組: docker-desktop
預設版本: 2
PS C:\WINDOWS\system32> wsl -l -v
  NAME              STATE           VERSION
* docker-desktop    Stopped         2
```

---

## ✅ 最終解決方案

由於 Docker 使用 WSL2，**不需要啟用 Hyper-V**，只需保留 Port 1433 防止 WSL2 動態佔用：
```powershell
# Step 1 — 保留 Port 1433
netsh int ipv4 add excludedportrange protocol=tcp startport=1433 numberofports=1

# Step 2 — 確認保留成功（清單中應出現 1433 ~ 1433）
netsh int ipv4 show excludedportrange protocol=tcp

# Step 3 — 啟動 SQL Server
Start-Service MSSQLSERVER
```

---

## 💡 技術重點

| 項目 | 結論 |
|---|---|
| Docker 後端 | WSL2（非 Hyper-V） |
| 是否需要啟用 Hyper-V | ❌ 不需要 |
| 衝突原因 | WSL2 動態保留 Port 搶佔 1433 |
| 解決方式 | `netsh excludedportrange` 強制預留 Port 1433 |
| 是否需要重開機 | ❌ 不需要 |

---

## 🔮 未來注意事項

> 若日後重裝系統、重裝 Docker 或 WSL2 更新後，記得重新執行 `netsh int ipv4 add excludedportrange` 指令，確保 Port 1433 持續被保留！