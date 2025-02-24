[ASP.NET Core MVC 中的模型驗證 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#ivalidatableobject)

資料模型類別繼承 IValidatableObject 介面：
```csharp
public class todoListPostUpDto : IValidatableObject
{
	public string Name { get; set; }

	[Required(ErrorMessage = "請設定值")]
	public bool Enable { get; set; }

	[Required(ErrorMessage = "請設定值")]
	public int Orders { get; set; }

	public List<UploadFilePostDto> UploadFiles { get; set; }

  //以下Function是由 IValidatableObject 介面要求實作的，可自訂驗證規則
  //驗證失敗時可一次回傳所有驗證失敗的原因
	public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
	{
		todoContext context = (todoContext)validationContext.GetService(typeof(todoContext));

		if (context.TodoLists.Any(m => m.Name == Name))
			yield return new ValidationResult("不能設定同樣的代辦事項");

		if (Orders < 7)
			yield return new ValidationResult("Orders不能小於7");

	}
}

```