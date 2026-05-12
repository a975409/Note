
## 📋 前置準備

### 1. 安裝必要工具

| 工具 | 下載位置 | 說明 |
|------|----------|------|
| **.NET SDK** | [dotnet.microsoft.com](https://dotnet.microsoft.com/download) | 建議 .NET 8 或 9 |
| **VS Code** | [code.visualstudio.com](https://code.visualstudio.com) | 主要編輯器 |
| **C# Dev Kit** | VS Code 擴充功能 | 微軟官方擴充套件 |

### 2. 安裝 VS Code 擴充套件

在 VS Code 按 `Ctrl+Shift+X`，搜尋並安裝：

- ✅ **C# Dev Kit**（微軟官方，包含 C# 語法支援）
- ✅ **C#**（OmniSharp，若 Dev Kit 未包含）
- ✅ **.NET Install Tool**（自動安裝相依套件）

---

## 🚀 建立並執行新專案

### 方法一：使用終端機指令（推薦）

```bash
# 建立新的 Web API 專案
dotnet new webapi -n MyProject

# 進入專案資料夾
cd MyProject

# 用 VS Code 開啟
code .

# 執行專案
dotnet run
```

### 方法二：使用 VS Code 命令面板

1. 按 `Ctrl+Shift+P`
2. 輸入 `.NET: New Project`
3. 選擇專案類型（如 `ASP.NET Core Web API`）
4. 選擇資料夾與輸入專案名稱

---

## ▶️ 執行專案的三種方式

### 方式 1：終端機直接執行

```bash
# 一般執行
dotnet run

# 指定環境
dotnet run --environment Development

# 監看模式（存檔自動重啟，開發時推薦）
dotnet watch run
```

### 方式 2：按 F5 偵錯執行

1. 開啟專案資料夾後，VS Code 通常會自動偵測並提示產生 `launch.json`
2. 若沒有自動產生，按 `Ctrl+Shift+P` → 輸入 `.NET: Generate Assets for Build and Debug`
3. 按 **F5** 即可啟動並附加偵錯器

### 方式 3：使用執行按鈕

- 點選左側 **執行與偵錯** 圖示（`Ctrl+Shift+D`）
- 選擇設定後按 ▶️ 綠色播放鍵

---

## 📁 重要設定檔說明

### `.vscode/launch.json`（偵錯設定）

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": ".NET Core Launch (web)",
      "type": "coreclr",
      "request": "launch",
      "program": "${workspaceFolder}/bin/Debug/net8.0/MyProject.dll",
      "args": [],
      "cwd": "${workspaceFolder}",
      "env": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "https://localhost:5001;http://localhost:5000"
      }
    }
  ]
}
```

### `.vscode/tasks.json`（建置任務）

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "build",
      "command": "dotnet",
      "type": "process",
      "args": ["build", "${workspaceFolder}"],
      "problemMatcher": "$msCompile"
    }
  ]
}
```

---

## 🌐 確認執行成功

執行後開啟瀏覽器：

| 頁面 | 網址 |
|------|------|
| 首頁 | `https://localhost:5001` |
| Swagger UI | `https://localhost:5001/swagger` |
| HTTP | `http://localhost:5000` |

---

## ⚠️ 常見問題排解

### ❌ 憑證信任問題（HTTPS 警告）
```bash
dotnet dev-certs https --trust
```

### ❌ 找不到 `dotnet` 指令
- 確認 .NET SDK 已安裝：`dotnet --version`
- 重新開啟終端機或 VS Code

### ❌ 埠號被佔用
在 `appsettings.Development.json` 修改埠號：
```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": { "Url": "http://localhost:5050" },
      "Https": { "Url": "https://localhost:5051" }
    }
  }
}
```

### ❌ `launch.json` 路徑錯誤
確認 `program` 路徑中的 `net8.0` 與你的 `.csproj` 中 `<TargetFramework>` 一致。

---

## 💡 開發小技巧

- **`dotnet watch run`** — 存檔即自動重啟，開發效率最高
- **`Ctrl+`` `** — 在 VS Code 內開啟終端機
- **`Ctrl+.`** — 快速修正（Quick Fix），自動補 using
- **F12** — 跳至定義；**Shift+F12** — 查看所有參考