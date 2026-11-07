


## 簡短結論

| 問題                               | 答案                                               |
| -------------------------------- | ------------------------------------------------ |
| 相同參數的**環境變數**會覆蓋 User Secrets 嗎? | **會**,相同參數的環境變數會覆蓋 User Secrets                  |
| 上線後主機或 IIS 的環境變數會覆蓋嗎?            | **不會有這個問題**,因為正式環境通常不會載入 User Secrets,環境變數會直接生效。 |

## 1. 組態來源的預設優先順序

ASP.NET Core 使用 `WebApplication.CreateBuilder()` 或 `Host.CreateDefaultBuilder()` 時,組態來源**由低到高**載入,後載入的會覆蓋先載入的:

1. `appsettings.json`
2. `appsettings.{Environment}.json`
3. **User Secrets**(僅限 `Development` 環境)
4. **環境變數** [[環境變數不只是「專案內」的]]
5. 命令列參數

## 2. 專案內的環境變數

以下設定都屬於環境變數,都會覆蓋 User Secrets:
- `launchSettings.json` 的 `environmentVariables`
- Visual Studio 的偵錯屬性
- 系統或使用者層級的環境變數

例如:

```json
// launchSettings.json
"environmentVariables": {
  "ASPNETCORE_ENVIRONMENT": "Development",
  "ConnectionStrings__Default": "Server=env-server;..."
}
```

如果 User Secrets 也有 `ConnectionStrings:Default`,執行時會取用**環境變數**的值。

階層用的分隔符號:

- 環境變數使用雙底線 `__`,例如 `ConnectionStrings__Default`。
- 在 Linux 上也可以使用 `:`,但 `__` 在所有平台都通用。

## 3. 上線後(主機 / IIS)

### User Secrets 只在 Development 載入

預設只有 `ASPNETCORE_ENVIRONMENT=Development` 時才會載入 User Secrets。

正式環境(`Production`)不會載入,也不應該依賴它。主機或 IIS 的環境變數會直接作為該參數的來源。

### 正式環境建議的設定方式

- **IIS**:在 `web.config` 的 `<environmentVariables>` 設定,或使用應用程式集區的環境變數。
- **Azure App Service**:使用「組態/應用程式設定」。
- **Docker / Kubernetes**:使用 env 或 Secret。
- **雲端**:使用 Azure Key Vault、AWS Secrets Manager 等。

IIS 的 `web.config` 範例:

```xml
<aspNetCore processPath="dotnet" arguments=".\MyApp.dll" hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ConnectionStrings__Default" value="..." />
  </environmentVariables>
</aspNetCore>
```

## 4. 注意事項

- **如果在正式主機上把 `ASPNETCORE_ENVIRONMENT` 設成 `Development`**,User Secrets 仍會載入,但該主機上不會有對應的 secrets.json 檔案,所以不會有實際影響。而且不建議這樣做。
- 若要確認最終的組態值,可以在程式中輸出:

```csharp
var value = builder.Configuration["ConnectionStrings:Default"];
```

或使用 `(builder.Configuration as IConfigurationRoot).GetDebugView()` 查看每個值來自哪個來源。
- 如果想改變優先順序,可以自行呼叫 `builder.Configuration.AddUserSecrets<Program>()` 或 `AddEnvironmentVariables()`,後加入的優先。

## 總結

- 同名參數:**命令列 > 環境變數 > User Secrets > appsettings.{Env}.json > appsettings.json**
- 開發時若 User Secrets 的值沒生效,先檢查是否有同名的環境變數(例如 `launchSettings.json` 或系統環境變數)。
- 上線後 User Secrets 不會載入,以主機、IIS 或雲端服務的環境變數為準。