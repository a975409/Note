
# 建置 .NET 專案的指令

以下是使用 .NET CLI 建置 .NET 10 專案的常用指令：

---

## 🔨 基本建置指令

```bash
# 建置專案
dotnet build

# 建置特定 .sln 檔案
dotnet build HaikouAPI.sln

# 建置特定 .csproj 檔案
dotnet build ./YourProject/YourProject.csproj
```

---

## ⚙️ 常用建置選項

```bash
# 指定組態 (Debug / Release)
dotnet build HaikouAPI.sln --configuration Release
dotnet build HaikouAPI.sln -c Debug

# 指定目標框架
dotnet build HaikouAPI.sln --framework net10.0

# 清除後重新建置
dotnet clean HaikouAPI.sln && dotnet build HaikouAPI.sln

# 還原套件後建置
dotnet restore HaikouAPI.sln && dotnet build HaikouAPI.sln

# 不還原套件直接建置（加速）
dotnet build HaikouAPI.sln --no-restore
```

---

## 📦 發佈指令

```bash
# 發佈 Release 版本
dotnet publish HaikouAPI.sln -c Release

# 指定輸出資料夾
dotnet publish HaikouAPI.sln -c Release -o ./publish

# 發佈為單一執行檔
dotnet publish HaikouAPI.sln -c Release --self-contained true -r win-x64 /p:PublishSingleFile=true
```

---

## 🔍 確認 .NET 版本

```bash
# 確認目前安裝的 SDK 版本
dotnet --version

# 列出所有已安裝的 SDK
dotnet --list-sdks

# 列出所有已安裝的 Runtime
dotnet --list-runtimes
```

# 透過指令執行 .NET 專案

---

## ▶ 基本執行指令

```bash
# 執行專案（開發模式）
dotnet run

# 指定專案檔執行
dotnet run --project ./HaikouAPI/HaikouAPI.csproj
```

---

## ⚙️ 常用執行選項

```bash
# 指定環境變數
dotnet run --project ./HaikouAPI/HaikouAPI.csproj --environment Development
dotnet run --project ./HaikouAPI/HaikouAPI.csproj --environment Production

# 指定組態
dotnet run --project ./HaikouAPI/HaikouAPI.csproj --configuration Release

# 指定 URL / Port
dotnet run --project ./HaikouAPI/HaikouAPI.csproj --urls "http://localhost:5050"

# 同時指定多個 URL
dotnet run --project ./HaikouAPI/HaikouAPI.csproj --urls "http://localhost:5050;https://localhost:7050"
```

---

## 🚀 執行已發布的專案

```bash
# 先發布
dotnet publish ./HaikouAPI/HaikouAPI.csproj -c Release -o ./publish

# 執行發布後的 dll
dotnet ./publish/HaikouAPI.dll

# 指定 URL 執行
dotnet ./publish/HaikouAPI.dll --urls "http://localhost:5050"
```

---

## 🔄 熱重載（開發時推薦）

```bash
# 啟用熱重載，修改程式碼後自動重新載入
dotnet watch run --project ./HaikouAPI/HaikouAPI.csproj

# 指定 URL + 熱重載
dotnet watch run --project ./HaikouAPI/HaikouAPI.csproj --urls "http://localhost:5050"
```

---

## 🌐 環境變數設定方式

### Windows (CMD)
```cmd
set ASPNETCORE_ENVIRONMENT=Development
dotnet run --project ./HaikouAPI/HaikouAPI.csproj
```

### Windows (PowerShell)
```powershell
$env:ASPNETCORE_ENVIRONMENT = "Development"
dotnet run --project ./HaikouAPI/HaikouAPI.csproj
```

### Linux / macOS
```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run --project ./HaikouAPI/HaikouAPI.csproj
```

---

## 📋 指令對照表

| 情境 | 指令 |
|------|------|
| 開發執行 | `dotnet run` |
| 熱重載開發 | `dotnet watch run` |
| 執行發布檔 | `dotnet ./publish/HaikouAPI.dll` |
| 指定 Port | `--urls "http://localhost:5050"` |
| 指定環境 | `--environment Development` |

---

> ✅ **建議**
> - 開發時使用 `dotnet watch run` 啟用熱重載，提升開發效率
> - 正式環境使用 `dotnet publish` 後再執行 `.dll`，效能較佳

# 發布指定專案的指令

在 Solution 中發布**特定專案**，有以下幾種方式：

---

## 🎯 直接指定 `.csproj` 發布

```bash
# 基本語法
dotnet publish ./專案資料夾/專案名稱.csproj

# 範例
dotnet publish ./HaikouAPI/HaikouAPI.csproj -c Release -o ./publish
```

---

## 📁 常用發布情境

### ▶ 一般發布（框架依賴）
```bash
dotnet publish ./HaikouAPI/HaikouAPI.csproj \
  -c Release \
  -o ./publish
```

### ▶ 自包含發布（不需安裝 Runtime）
```bash
dotnet publish ./HaikouAPI/HaikouAPI.csproj \
  -c Release \
  -r win-x64 \
  --self-contained true \
  -o ./publish
```

### ▶ 單一執行檔
```bash
dotnet publish ./HaikouAPI/HaikouAPI.csproj \
  -c Release \
  -r win-x64 \
  --self-contained true \
  /p:PublishSingleFile=true \
  -o ./publish
```

### ▶ 指定目標框架
```bash
dotnet publish ./HaikouAPI/HaikouAPI.csproj \
  -c Release \
  -f net10.0 \
  -o ./publish
```

---

## 🖥️ 常見 Runtime ID（`-r` 參數）

| 平台 | Runtime ID |
|------|-----------|
| Windows x64 | `win-x64` |
| Windows x86 | `win-x86` |
| Linux x64 | `linux-x64` |
| Linux ARM64 | `linux-arm64` |
| macOS x64 | `osx-x64` |
| macOS ARM64 | `osx-arm64` |

---

## 💡 小技巧

```bash
# 發布前先清除舊檔
dotnet clean ./HaikouAPI/HaikouAPI.csproj
dotnet publish ./HaikouAPI/HaikouAPI.csproj -c Release -o ./publish

# 跳過還原步驟（已還原過）
dotnet publish ./HaikouAPI/HaikouAPI.csproj -c Release --no-restore -o ./publish
```

> ✅ **建議**：發布 API 專案通常使用 `-c Release` + 指定 `-o` 輸出資料夾，方便部署到伺服器。