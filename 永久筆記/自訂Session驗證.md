**Cookie與Session**

[https://ithelp.ithome.com.tw/articles/10249151](https://ithelp.ithome.com.tw/articles/10249151)

cookie是用來儲存Session Id，而對應到伺服器端的Session

cookie是儲放在本地端，Session則是存放在伺服器，主要是儲放使用者資訊

驗證流程大致如下：

1. 使用者登入，並將使用者資訊(Claim)添加至Session
    
2. 每次Request再從Session取出使用者資訊(Claim)，並產生 AuthenticationTicket
    
3. 自訂驗證方案
    
    - 建立 TestAuthenticationOptions.cs，設定自訂驗證的相關參數
        
        ```csharp
        public class TestAuthenticationOptions
        {
        	/// <summary>
        	/// 未登入時會自動導到這個網址
        	/// </summary>
        	public string LoginPath { get; set; } = "/api/Login/NoLogin";
        
        	/// <summary>
        	/// 沒有權限時會自動導到這個網址
        	/// </summary>
        	public string ReturnUrlKey { set; get; } = "/api/Login/NoAccess";
        }
        ```
        
    - 自訂驗證方案，建立 TestAuthenticationHandler
        
        ※ 以下例子Session裡面存放使用者名稱([ClaimTypes.Name](http://ClaimTypes.Name))
        
        ```csharp
        public class TestAuthenticationHandler : IAuthenticationSignInHandler
        {
        	//驗證方案名稱，在驗證和授權功能會用到
        	public const string TEST_SCHEM_NAME = "some_authen";
        
        	public TestAuthenticationOptions Options { get; private set; }
        
        	public TestAuthenticationHandler(IOptions<TestAuthenticationOptions> opt)
        	{
        		Options = opt.Value;
        	}
        
        	public HttpContext HttpContext { get; private set; }
        	public AuthenticationScheme Scheme { get; private set; }
        
        	/// <summary>
        	/// 驗證過程，主要是檢查Session是否有存放任何Key
        	/// </summary>
        	/// <returns>驗證結果</returns>
        	public Task<AuthenticateResult> AuthenticateAsync()
        	{
        		if (Scheme.Name != TEST_SCHEM_NAME)
        		{
        			return Task.FromResult(AuthenticateResult.Fail("驗證方案不匹配"));
        		}
        
        		if (!HttpContext.Session.Keys.Any())
        		{
        			return Task.FromResult(AuthenticateResult.Fail("Session無效"));
        		}
        
        		#region 驗證通過
            //以下流程就是
        
            //要注意指定驗證方案
        		ClaimsIdentity id = new ClaimsIdentity(TEST_SCHEM_NAME);
        
        		//從Session獲取使用者資訊
        		foreach (var key in HttpContext.Session.Keys)
        		{
        			//要取出多個權限(Roles)，要將其做反序列化的動作
        			if (key == ClaimTypes.Role)
        			{
        				string[] Roles = JsonSerializer.Deserialize<string[]>(HttpContext.Session.GetString(key));
        			
        				foreach (string item in Roles)
        				{
        					id.AddClaim(new Claim(ClaimTypes.Role, item));
        				}
        			}
        			else
        			{
        				id.AddClaim(new Claim(key, HttpContext.Session.GetString(key)));
        			}
        		}
        
        		ClaimsPrincipal prcp = new ClaimsPrincipal(id);
        
            //要注意指定驗證方案
        		AuthenticationTicket ticket = new AuthenticationTicket(prcp, TEST_SCHEM_NAME);
        		return Task.FromResult(AuthenticateResult.Success(ticket));
        
        		#endregion
        	}
        
        	/// <summary>
        	/// 一旦驗證失敗，就會呼叫這個方法，向客戶端索要驗證資訊。這裡需要的驗證資訊是輸入使用者名稱和密碼。所以就會倒到登入畫面
        	/// </summary>
        	/// <param name="properties"></param>
        	/// <returns></returns>
        	public Task ChallengeAsync(AuthenticationProperties properties)
        	{
        		HttpContext.Response.Redirect($"{Options.LoginPath}?{Options.ReturnUrlKey}={HttpContext.Request.Path}");
        		return Task.CompletedTask;
        	}
        
        	/// <summary>
        	/// 禁止訪問時用，可以直接呼叫 HttpContext 的 ForbidAsync 方法。
        	/// </summary>
        	/// <param name="properties"></param>
        	/// <returns></returns>
        	public Task ForbidAsync(AuthenticationProperties properties)
        	{
            //跳轉到沒有權限的頁面
        		HttpContext.Response.Redirect(Options.ReturnUrlKey);
            return Task.CompletedTask;
        	}
        
        	/// <summary>
        	/// 初始化時用，一般可以從這裡獲取當前請求關聯的 HttpContext ，以及正在被使用的驗證方案資訊。
        	/// </summary>
        	/// <param name="scheme"></param>
        	/// <param name="context"></param>
        	/// <returns></returns>
        	public Task InitializeAsync(AuthenticationScheme scheme, HttpContext context)
        	{
        		HttpContext = context;
        		Scheme = scheme;
        		return Task.CompletedTask;
        	}
        
        	/// <summary>
        	/// 登入，把使用者名稱放入 Session
        	/// </summary>
        	/// <param name="user"></param>
        	/// <param name="properties"></param>
        	/// <returns></returns>
        	public Task SignInAsync(ClaimsPrincipal user, AuthenticationProperties properties)
        	{
        		List<string> Roles = new List<string>();
        
        		//將在LoginController的Login設定的使用者資訊(Claims)，都存到session裡面，之後每次的Request只要從Session取出使用者資訊就行
        		foreach (var claim in user.Claims)
        		{
        			if (claim.Type == ClaimTypes.Role)
        			{
        				Roles.Add(claim.Value);
        			}
        			else
        			{
        				HttpContext.Session.SetString(claim.Type, claim.Value);
        			}
        		}
        		
        		//以Key=ClaimTypes.Role為例，如果要設定多種權限(Roles)，則要將其轉成JSON字串後再存入Cookie
        		string jsonRolesString = JsonSerializer.Serialize(Roles.ToArray());
        		
        		HttpContext.Session.SetString(ClaimTypes.Role, jsonRolesString);
        
        		return Task.CompletedTask;
        	}
        
        	/// <summary>
        	/// 登出，將Session 中的使用者名稱刪除
        	/// </summary>
        	/// <param name="properties"></param>
        	/// <returns></returns>
        	public Task SignOutAsync(AuthenticationProperties properties)
        	{
            //移除存在Session裡面所有使用者資訊
        		foreach (var key in HttpContext.Session.Keys)
            {
               HttpContext.Session.Remove(key);
            }
         
        		return Task.CompletedTask;
        	}
        }
        ```
        
4. 在Startup.cs註冊相關服務：
    
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    	#region 啟用Session功能
    	services.AddSession(options =>
    	{
        //修改合理的 Session 到期時間
    		options.IdleTimeout = TimeSpan.FromMinutes(5);
    		
        //限制只有在 HTTPS 連線的情況下，才允許使用 Session。如此一來變成加密連線，就不容易被攔截。
        options.Cookie.SecurePolicy = CookieSecurePolicy.None;
    		
        //如果 cookie 必須無法由用戶端腳本(JavaScript)存取，則為 true;否則為 false。
        options.Cookie.HttpOnly = true;
    		
        //指出此 cookie 是否為應用程式正確運作的必要項。 若為 true，則可能略過同意原則檢查
        options.Cookie.IsEssential = true;
    
        //更改Session 名稱
    		options.Cookie.Name = "session.user";
    	});
    
      //將Session放置到伺服器的內建記憶體
      services.AddDistributedMemoryCache();
      #endregion
    
      #region 自訂驗證方案
      
      //以下呼應到DI注入的IOptions<TestAuthenticationOptions> opt，請看TestAuthenticationHandler.cs
      services.AddOptions<TestAuthenticationOptions>();
    
    	// 新增驗證功能
    	services.AddAuthentication(option =>
    	{
    		// 新增我們自定義的驗證方案名
    		option.AddScheme<TestAuthenticationHandler>(TestAuthenticationHandler.TEST_SCHEM_NAME, null);
    	});
      #endregion
    	
      #region 改寫內建授權功能，並自訂授權策略
    	// 新增授權功能
    	services.AddAuthorization(option =>
    	{
        // 註冊授權策略，名為“demo2”
    		option.AddPolicy("demo2", c =>
    		{
    			// 與我們前面定義的驗證方案繫結
    			// 授權過程跟隨該驗證後發生
          // 要注意指定驗證方案
    			c.AddAuthenticationSchemes(TestAuthenticationHandler.TEST_SCHEM_NAME);
    	
    			// 要求存在已登入使用者的標識
    			c.RequireAuthenticatedUser();
    		});
    	});
      #endregion
    
      services.AddMvc(options =>
    	{
    		//所有controller都要驗證
    		options.Filters.Add(new AuthorizeFilter());
    	});
    }
    ```
    
5. 在Startup.cs 插入這些中介軟體到HTTP管道
    
    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
    
      //放到app.UseRouting()後面
      app.UseSession();
      app.UseAuthentication();
      app.UseAuthorization();
    }
    ```
    
6. 在controller實作登入功能：
    
    ```csharp
    [HttpPost]
    public async Task<IActionResult> Login(AccountDto value)
    {
    	var result = _todoContext.Employees.Where(m => m.Account == value.Account && m.Password == value.Password).FirstOrDefault();
    
    	if (result == null)
    		return StatusCode(302, "登入失敗，帳號密碼錯誤");
    
      //建立使用者資訊
    	var claims = new List<Claim> {
    					 new Claim(ClaimTypes.Name,result.Account),//使用者名稱
    					 new Claim("FullName",result.Name),
    					 new Claim("EmployeeId",result.EmployeeId.ToString())
    					};
    
    	//取得權限(Roles)
    	var Roles = _todoContext.Roles.Where(m => m.EmployeeId == result.EmployeeId);
      
      //新增權限(Roles)
    	foreach (var role in Roles)
    	{
    		claims.Add(new Claim(ClaimTypes.Role, role.Name));
    	}
    
      // 要注意指定驗證方案
    	var claimsIdentity = new ClaimsIdentity(claims, TestAuthenticationHandler.TEST_SCHEM_NAME);
    	await HttpContext.SignInAsync(TestAuthenticationHandler.TEST_SCHEM_NAME, new ClaimsPrincipal(claimsIdentity));
    	return Ok("登入成功");
    }
    ```
    
7. 在controller實作登出功能：
    
    ```csharp
    [HttpDelete]
    public async Task<IActionResult> Logout()
    {
      // 要注意指定驗證方案
    	await HttpContext.SignOutAsync(TestAuthenticationHandler.TEST_SCHEM_NAME);
    	return Ok("已登出");
    }
    ```
    
8. 特別注意的是要使用微軟內建授權功能，要額外指定授權策略
    
    ```csharp
    [Authorize("demo2")] //額外指定授權策略
    ```
    

**[ASP.NET](http://ASP.NET) Core 中的工作階段和狀態管理**

[https://docs.microsoft.com/zh-tw/aspnet/core/fundamentals/app-state?view=aspnetcore-6.0](https://docs.microsoft.com/zh-tw/aspnet/core/fundamentals/app-state?view=aspnetcore-6.0)

Session驗證實作參考網址：

[https://iter01.com/658217.html](https://iter01.com/658217.html)

[[.NET Core] ASP .NET Core 3.1 驗證與授權(三)-Cookie 驗證實例 - 世情如紙 (tigernaxo.com)](https://blogger.tigernaxo.com/post/dotnetcore31/auth/auth_guild_3/)

[https://blog.miniasp.com/post/2019/12/25/asp-net-core-3-cookie-based-authentication](https://blog.miniasp.com/post/2019/12/25/asp-net-core-3-cookie-based-authentication)