[ASP.NET Core MVC 中的模型驗證 | Microsoft Learn](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#custom-attributes)

1. 建立一個類別，並繼承 ValidationAttribute 類別：
    ```csharp
    protected override ValidationResult IsValid(object value, ValidationContext validationContext)
    {
    	//取得資料庫物件
    	TodoContext _todoContext = (TodoContext)validationContext.GetService(typeof(TodoContext));
    
    	string todoName = (string)value;
    
    	var result = _todoContext.TodoLists.Where(m => m.Name == todoName);
    
    	//ObjectInstance為正在驗證屬性的類別物件
    	if (validationContext.ObjectInstance.GetType() == typeof(TodoListPutDto))
    	{
    		TodoListPutDto todoListPutDto = (TodoListPutDto)validationContext.ObjectInstance;
    		result = result.Where(m => m.TodoId != todoListPutDto.TodoId);
    	}
    
    	if (result != null && result.Count() > 0)
    		return new ValidationResult("已存在相同代辦事項");
    
    	return ValidationResult.Success;
    }
    
    ```
    
2. 接著就可以直接在Model裡面針對要驗證的欄位或類別上方套用標籤屬性：
    ```csharp
    public class TodoListPutDto
    {
    	[TodoName]
    	public string Name { get; set; }
    	public bool? Enable { get; set; }
    	public int? Orders { get; set; }
    	public DateTime? StartTime { get; set; }
    	public DateTime? EndTime { get; set; }
    }
    
    ```
    
3. 如果要開放讓使用者自訂錯誤訊息，則可以使用以下方法
    ```csharp
    public class TodoNameAttribute : ValidationAttribute
    {
    	//新增錯誤訊息的欄位
    	private string _errorMessage;
    
    	//新增錯誤訊息的屬性，讓使用者可以自己指定屬性名稱做設定
    	public string errorMessage
    	{
    		get { return _errorMessage; }
    		set
    		{
    			if (!string.IsNullOrEmpty(value.ToString()))
    			{
    				_errorMessage = value;
    			}
    		}
    	}
    
    	public TodoNameAttribute(string errorMessage = "")
    	{
    		_errorMessage = errorMessage;
    	}
    
    	protected override ValidationResult IsValid(object value, ValidationContext validationContext)
    	{
    		//取得資料庫物件
    		TodoContext _todoContext = (TodoContext)validationContext.GetService(typeof(TodoContext));
    
    		string todoName = (string)value;
    
    		var result = _todoContext.TodoLists.Where(m => m.Name == todoName);
    
    		if (result != null && result.Count() > 0)
    			return new ValidationResult(_errorMessage);
    
    		return ValidationResult.Success;
    	}
    }
    
    ```
    
4. 外部呼叫方式：
    ```csharp
    public class TodoListPutDto
    {
    	[TodoName(errorMessage = "已有相同的代辦事項")]
    	public string Name { get; set; }
    	public bool? Enable { get; set; }
    	public int? Orders { get; set; }
    	public DateTime? StartTime { get; set; }
    	public DateTime? EndTime { get; set; }
    }
    
    ```