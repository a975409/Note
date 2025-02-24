[開發者必備知識 - HTTP認證（HTTP Authentication）](https://carsonwah.github.io/http-authentication.html)
[JWT身分驗證機制](https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E9%80%8F%E9%81%8E-jwt-%E5%AF%A6%E4%BD%9C%E9%A9%97%E8%AD%89%E6%A9%9F%E5%88%B6-2e64d72594f8)

1. 在appsettings.json新增以下程式碼：

```json
"JWT": {
    "KEY": "ASDZXASDHAUISDHASDOHAHSDUAHDS",
    "Issuer": "todo.com",
    "Audience": "my"
}

```

1. 在 Startup.cs 新增以下程式碼：
    
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    	services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    	.AddJwtBearer(options =>
    	{
    		options.TokenValidationParameters = new TokenValidationParameters
    		{
    				ValidateIssuer = true,
    				ValidIssuer = Configuration["Jwt:Issuer"],
    				ValidateAudience = true,
    				ValidAudience = Configuration["Jwt:Audience"],
    				ValidateLifetime = true,//逾期後是否要將其登出
    				ClockSkew= TimeSpan.Zero,//設定登入狀態逾期後的緩衝時間
    				IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(Configuration["JWT:KEY"]))
    		};
    	});
    }
    
    ```
    
2. 在controller 實作JWT登入功能：
    
    ```csharp
    //config DI注入
    private readonly IConfiguration _configuration;
    public LoginController(IConfiguration configuration) {
    			_configuration = configuration;
    }
    
    [HttpPost("jwtLogin")]
    public IActionResult jwtLogin(AccountDto value)
    {
    	var result = _todoContext.Employees.Where(m => m.Account == value.Account && m.Password == value.Password).FirstOrDefault();
    
    	if (result == null)
    		return StatusCode(302, "登入失敗，帳號密碼錯誤");
    
    	var claims = new List<Claim> {
    					 new Claim(JwtRegisteredClaimNames.Email,result.Account),
    					 new Claim("FullName",result.Name),
    					 new Claim(JwtRegisteredClaimNames.NameId,result.EmployeeId.ToString())
    					};
    
    	var Roles = _todoContext.Roles.Where(m => m.EmployeeId == result.EmployeeId);
    
    	foreach (var role in Roles)
    	{
    		claims.Add(new Claim(ClaimTypes.Role, role.Name));
    	}
    
    	//取出appsettings.json裡的KEY處理
    	var securityKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["JWT:KEY"]));
    
    	//設定jwt相關資訊
    	var jwt = new JwtSecurityToken
    	(
    		issuer: _configuration["JWT:Issuer"],
    		audience: _configuration["JWT:Audience"],
    		claims: claims,
    		expires: DateTime.Now.AddMinutes(30),
    		signingCredentials: new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256)
    	);
    
    	//產生JWT Token
    	var token = new JwtSecurityTokenHandler().WriteToken(jwt);
    
    	//回傳JWT Token給認證通過的使用者
    	return Ok(token);
    }
    
    ```
    
3. 如果要通過驗證，就在Headers新增 Authorization 這個Key值，對應的value則設定為：bearer+"空格”+JWT Token
    ![[Pasted image 20250104184634.png]]