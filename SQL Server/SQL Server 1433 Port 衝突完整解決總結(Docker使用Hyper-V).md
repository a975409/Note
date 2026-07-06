
## 🔍 問題差異

當 Docker 使用 **Hyper-V 後端**時，Hyper-V 啟動會**動態保留一段 Port 範圍**，這個範圍是隨機的，很容易把 1433 包進去，造成 SQL Server 無法啟動。

---

## ✅ 解決步驟

關鍵原則：**必須在 Hyper-V 啟動之前先保留 Port 1433**

```powershell
# Step 1 — 先停用 Hyper-V
dism /Online /Disable-Feature /FeatureName:Microsoft-Hyper-V-All

# Step 2 — 重開機（讓 Hyper-V 完全停止）
Restart-Computer
```

重開機後：

```powershell
# Step 3 — 保留 Port 1433（此時 Hyper-V 已停用，保留才有效）
netsh int ipv4 add excludedportrange protocol=tcp startport=1433 numberofports=1

# Step 4 — 確認保留成功
netsh int ipv4 show excludedportrange protocol=tcp

# Step 5 — 重新啟用 Hyper-V
dism /Online /Enable-Feature /FeatureName:Microsoft-Hyper-V-All /All

# Step 6 — 重開機
Restart-Computer
```

重開機後確認：

```powershell
# 確認 SQL Server 正常運作
Get-Service MSSQLSERVER

# 確認 Port 1433 被 SQL Server 佔用
netstat -ano | findstr ":1433"

# 確認 Docker 正常
docker ps
```

---

## ⚠️ 為什麼要先停用 Hyper-V 再保留？

```
Hyper-V 運作中
    ↓
動態保留 Port 範圍（可能包含 1433）
    ↓
此時執行 netsh excludedportrange 無效！
    ↓
必須在 Hyper-V 停用狀態下才能成功保留
```

> 💡 `netsh int ipv4 add excludedportrange` 只在 **Hyper-V 停用狀態**下才能成功寫入，這是 Windows 的限制！

---

## 📋 兩種後端比較總結

| | WSL2 後端（你的情況） | Hyper-V 後端 |
|---|---|---|
| 需要停用/重啟 Hyper-V | ❌ 不需要 | ✅ 需要 |
| 需要重開機 | ❌ 不需要 | ✅ 需要兩次 |
| 解決複雜度 | 🟢 簡單 | 🟡 較繁瑣 |
| Port 保留方式 | 相同 `netsh` 指令 | 相同 `netsh` 指令 |