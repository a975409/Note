
HTTP 400 回應的預設回應類型為 [ValidationProblemDetails](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.mvc.validationproblemdetails)。 下列回應本文是序列化類型的範例：
```JSON
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}

```


如果有新增一個自訂回應，並且沿用預設回應類型 [ValidationProblemDetails](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.mvc.validationproblemdetails)，則須回傳 [ValidationProblem](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.validationproblem) 方法，而不是[BadRequest](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.badrequest)：
```C#
[HttpGet(Name = "actionResult")]
public ActionResult actionResult()
{
    ModelState.AddModelError("smh_data.materials.country_of_origin", "Field is required.");

    return ValidationProblem();
}
```

