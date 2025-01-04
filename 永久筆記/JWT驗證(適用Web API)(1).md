[開發者必備知識 - HTTP認證（HTTP Authentication）](https://carsonwah.github.io/http-authentication.html)
JWT原理說明：[[JWT(JSON Web Token) — 原理介紹]]

[安裝Nuget套件](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.JwtBearer)

加入 `appsettings.json` 組態設定：
```JSON
{
  "JwtSettings": {
    "Issuer": "JwtAuthDemo",
    "SignKey": "01234567890123456789012345678901"
  }
}

```

新增 `JwtHelpers` 輔助類別：
```C#
using System.Security.Claims;
using System.Text;
using Microsoft.IdentityModel.JsonWebTokens;
using Microsoft.IdentityModel.Tokens;

public class JwtHelpers
{
    private readonly IConfiguration Configuration;

    public JwtHelpers(IConfiguration configuration)
    {
        this.Configuration = configuration;
    }

	//產生JWT Token
    public string GenerateToken(string userName, int expireMinutes = 30)
    {
        var issuer = Configuration.GetValue<string>("JwtSettings:Issuer");
        var signKey = Configuration.GetValue<string>("JwtSettings:SignKey");

        // Configuring "Claims" to your JWT Token
        var claims = new List<Claim>();

        // In RFC 7519 (Section#4), there are defined 7 built-in Claims, but we mostly use 2 of them.
        //claims.Add(new Claim(JwtRegisteredClaimNames.Iss, issuer));
        claims.Add(new Claim(JwtRegisteredClaimNames.Sub, userName)); // User.Identity.Name
        //claims.Add(new Claim(JwtRegisteredClaimNames.Aud, "The Audience"));
        //claims.Add(new Claim(JwtRegisteredClaimNames.Exp, DateTimeOffset.UtcNow.AddMinutes(30).ToUnixTimeSeconds().ToString()));
        //claims.Add(new Claim(JwtRegisteredClaimNames.Nbf, DateTimeOffset.UtcNow.ToUnixTimeSeconds().ToString())); // 必須為數字
        //claims.Add(new Claim(JwtRegisteredClaimNames.Iat, DateTimeOffset.UtcNow.ToUnixTimeSeconds().ToString())); // 必須為數字
        claims.Add(new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())); // JWT ID

        // The "NameId" claim is usually unnecessary.
        //claims.Add(new Claim(JwtRegisteredClaimNames.NameId, userName));

        // This Claim can be replaced by JwtRegisteredClaimNames.Sub, so it's redundant.
        //claims.Add(new Claim(ClaimTypes.Name, userName));

        // TODO: You can define your "roles" to your Claims.
        claims.Add(new Claim(ClaimTypes.Role, "Admin"));
        claims.Add(new Claim(ClaimTypes.Role, "Users"));

        var userClaimsIdentity = new ClaimsIdentity(claims);

        // Create a SymmetricSecurityKey for JWT Token signatures
        var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(signKey));

        // HmacSha256 MUST be larger than 128 bits, so the key can't be too short. At least 16 and more characters.
        // https://stackoverflow.com/questions/47279947/idx10603-the-algorithm-hs256-requires-the-securitykey-keysize-to-be-greater
        var signingCredentials = new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256Signature);

        // Create SecurityTokenDescriptor
        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Issuer = issuer,
            //Audience = issuer, // Sometimes you don't have to define Audience.
            //NotBefore = DateTime.Now, // Default is DateTime.Now
            //IssuedAt = DateTime.Now, // Default is DateTime.Now
            Subject = userClaimsIdentity,
            Expires = DateTime.Now.AddMinutes(expireMinutes),
            SigningCredentials = signingCredentials
        };

        // Generate a JWT, than get the serialized Token result (string)
        var tokenHandler = new JsonWebTokenHandler();
        var serializeToken = tokenHandler.CreateToken(tokenDescriptor);

        return serializeToken;
    }
}
```

Program.cs設定如下：
```C#
builder.Services.AddSingleton<JwtHelpers>();
builder.Services
    .AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        // 當驗證失敗時，回應標頭會包含 WWW-Authenticate 標頭，這裡會顯示失敗的詳細錯誤原因
        options.IncludeErrorDetails = true; // 預設值為 true，有時會特別關閉

        options.TokenValidationParameters = new TokenValidationParameters
        {
            // 透過這項宣告，就可以從 "sub" 取值並設定給 User.Identity.Name
            NameClaimType = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
            // 透過這項宣告，就可以從 "roles" 取值，並可讓 [Authorize] 判斷角色
            RoleClaimType = "http://schemas.microsoft.com/ws/2008/06/identity/claims/role",

            // 一般我們都會驗證 Issuer
            ValidateIssuer = true,
            ValidIssuer = builder.Configuration.GetValue<string>("JwtSettings:Issuer"),

            // 通常不太需要驗證 Audience
            ValidateAudience = false,
            //ValidAudience = "JwtAuthDemo", // 不驗證就不需要填寫

            // 一般我們都會驗證 Token 的有效期間
            ValidateLifetime = true,

            // 如果 Token 中包含 key 才需要驗證，一般都只有簽章而已
            ValidateIssuerSigningKey = false,

            // "1234567890123456" 應該從 IConfiguration 取得
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(builder.Configuration.GetValue<string>("JwtSettings:SignKey")))
        };
    });

builder.Services.AddAuthorization();

//請加入以下兩行在 `app.UseHttpsRedirection();` 後面：
app.UseAuthentication();
app.UseAuthorization();

```

實作 LoginController
```C#
namespace JwtAuthDemo.Controllers 
{ 
	using Microsoft.AspNetCore.Http; 
	using Microsoft.AspNetCore.Mvc; 
	
	[Route("api/[controller]")] 
	[ApiController] 
	public class LoginController : ControllerBase 
	{
		private readonly JwtHelpers _jwt;

		public LoginController(JwtHelpers jwt)
		{
			_jwt=jwt;
		}

		private bool ValidateUser(LoginViewModel login) => true; 
		
		/// <summary> 
		/// 登入並取得 JWT Token 
		/// </summary> 
		/// <param name="login"></param>
		/// <returns></returns> 
		[HttpPost(Name = "signin")]
		public IActionResult SignIn(LoginViewModel login) 
		{ 
			if (ValidateUser(login)) 
			{ 
				var token = _jwt.GenerateToken(login.UserName); 
				return Ok(new { token }); 
			} 
			else 
			{ 
				return BadRequest(); 
			} 
		}
	} 
}
```

實作JwtController
```C#
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

namespace WEBMVC.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    [Authorize]
    public class JwtController : ControllerBase
    {
        /// <summary>
        /// 取得 JWT Token 中的所有 Claims
        /// </summary>
        /// <returns></returns>
        //[HttpGet(Name = nameof(GetClaims))]
        [HttpGet("claims")]
        public IActionResult GetClaims()
        {
            return Ok(User.Claims.Select(p => new { p.Type, p.Value }));
        }

        /// <summary>
        /// 取得 JWT Token 中的使用者名稱
        /// </summary>
        /// <returns></returns>
        [HttpGet("username")]
        public IActionResult GetUserName()
        {
            return Ok(User.Identity?.Name);
        }

        /// <summary>
        /// 取得使用者是否擁有特定角色
        /// </summary>
        /// <param name="name"></param>
        /// <returns></returns>
        [HttpGet("isInRole")]
        public IActionResult IsInRole(string name)
        {
            return Ok(User.IsInRole(name));
        }

        /// <summary>
        /// 取得 JWT Token 中的 JWT ID
        /// </summary>
        /// <returns></returns>
        [HttpGet("jwtid")]
        public IActionResult GetJwtId()
        {
            return Ok(User.Claims.FirstOrDefault(p => p.Type == "jti")?.Value);
        }
    }
}

```

參考資料
https://www.jasperstudy.com/2023/04/aspnet-core-6-token-based-jwt.html#31-%E5%88%9D%E5%A7%8B%E5%8C%96%E5%B0%88%E6%A1%88
https://eandev.com/post/security/aspnet-core-authenticaiton-jwt/#%E4%BD%BF%E7%94%A8-jwt-authentication
https://blog.miniasp.com/post/2022/02/13/How-to-use-JWT-token-based-auth-in-aspnet-core-60