
1. 安裝nuget套件：Swashbuckle.AspNetCore
2. Program.cs設定如下：
```C#
builder.Services.AddSwaggerGen(options => {
    // 使用完整類型名稱作為 SchemaId，避免巢狀類別名稱衝突
    options.CustomSchemaIds(type =>
    {
        if (type.IsNested)
        {
            // 例如：DoctorDto_InsertDto、HospitalDto_InsertDto
            return $"{type.DeclaringType!.Name}_{type.Name}";
        }
        return type.Name;
    });

    // 載入 EVT.Web 的 XML 註解
    var webXmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
    var webXmlPath = Path.Combine(AppContext.BaseDirectory, webXmlFile);
    if (File.Exists(webXmlPath))
        options.IncludeXmlComments(webXmlPath);

	//以下如果有額外引用其他專案的dto，則需要額外引入
    // ✅ 額外載入 EVT.Domain 的 XML 註解
    var domainXmlFile = "EVT.Domain.xml";
    var domainXmlPath = Path.Combine(AppContext.BaseDirectory, domainXmlFile);
    if (File.Exists(domainXmlPath))
        options.IncludeXmlComments(domainXmlPath);

    // ✅ 額外載入 EVT.Domain 的 XML 註解
    var infrastructureXmlFile = "EVT.Infrastructure.xml";
    var infrastructureXmlPath = Path.Combine(AppContext.BaseDirectory, infrastructureXmlFile);
    if (File.Exists(infrastructureXmlPath))
        options.IncludeXmlComments(infrastructureXmlPath);
});

var app = builder.Build();

// 放在最前面
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
    });
}
```

3. 承上點，只要專案本身有swagger，或者是有dto的XML資訊需要提供給swagger使用，則需要針對*.csproj專案檔新增以下XML定義：
```XML
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>

  <!-- 關閉CS1591警告 -->
  <NoWarn>${NoWarn};1591</NoWarn>
</PropertyGroup>
```