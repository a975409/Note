### 常用選項與說明

|**選項**|**說明**|
|---|---|
|`Actort`|Actor，代表執行此操作的使用者或實體。|
|`Acr`|Authentication Context Class Reference，表示驗證上下文的類別參考。|
|`Amr`|Authentication Methods Reference，表示使用的驗證方法。|
|`Aud`|Audience，表示 JWT 的受眾，即目標使用者。|
|`AuthTime`|Authentication Time，表示使用者進行驗證的時間戳記（通常是 UNIX 時間）。|
|`Azp`|Authorized Party，表示被授權的主體（通常是應用程式的 ID）。|
|`CHash`|Code Challenge，表示授權碼的哈希值，用於授權碼流程的安全性。|
|`Exp`|Expiration Time，表示 JWT 的過期時間（UNIX 時間戳記）。|
|`Jti`|JWT ID，表示 JWT 的唯一識別碼，用於防止重放攻擊。|
|`Iat`|Issued At，表示 JWT 的簽發時間（UNIX 時間戳記）。|
|`Iss`|Issuer，表示簽發 JWT 的實體（通常是伺服器的 URL 或識別碼）。|
|`Nbf`|Not Before，表示 JWT 的生效時間（UNIX 時間戳記）。|
|`Sub`|Subject，表示 JWT 的主題，通常是使用者的唯一識別碼。|
|`NameId`|Name Identifier，表示使用者的名稱識別碼。|
|`Prn`|Principal，表示主要的主體。|

---

### 常用選項範例

以下是使用 `JwtRegisteredClaimNames` 定義 JWT 的範例：
```C#
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;

public class Program
{
    public static void Main(string[] args)
    {
        var claims = new[]
        {
            new Claim(JwtRegisteredClaimNames.Sub, "123456"), // 使用者的唯一識別碼
            new Claim(JwtRegisteredClaimNames.NameId, "JohnDoe"), // 使用者的名稱
            new Claim(JwtRegisteredClaimNames.Email, "john.doe@example.com"), // 使用者的電子郵件
            new Claim(JwtRegisteredClaimNames.Iat, DateTimeOffset.UtcNow.ToUnixTimeSeconds().ToString(), ClaimValueTypes.Integer64), // 簽發時間
            new Claim(JwtRegisteredClaimNames.Exp, DateTimeOffset.UtcNow.AddHours(1).ToUnixTimeSeconds().ToString(), ClaimValueTypes.Integer64), // 過期時間
            new Claim(JwtRegisteredClaimNames.Iss, "https://example.com"), // 簽發者
            new Claim(JwtRegisteredClaimNames.Aud, "https://example-app.com") // 受眾
        };

        foreach (var claim in claims)
        {
            Console.WriteLine($"{claim.Type}: {claim.Value}");
        }
    }
}
```

---
### 常見用途

1. **身份驗證與授權**：
    
    - 使用 `Sub` 表示使用者的唯一識別碼。
    - 使用 `Aud` 表示 JWT 的目標受眾。
    - 使用 `Iss` 表示 JWT 的簽發者。
2. **安全性相關**：
    
    - 使用 `Exp` 設定 JWT 的過期時間。
    - 使用 `Jti` 防止重放攻擊（唯一的 Token ID）。
3. **時間相關**：
    
    - 使用 `Iat` 表示 JWT 的簽發時間。
    - 使用 `Nbf` 表示 JWT 的生效時間。