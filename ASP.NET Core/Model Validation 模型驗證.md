[使用 Fluent Validation 來驗證參數吧](https://igouist.github.io/post/2022/03/newbie-7-fluent-validation/)
[重新執行驗證](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#rerun-validation)
[內建驗證屬性](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#built-in-attributes)
[System.ComponentModel.DataAnnotations 命名空間 | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/system.componentmodel.dataannotations?view=net-5.0)
[System.ComponentModel 命名空間 | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/system.componentmodel?view=net-5.0)
[[部分類別(1)]]
[[部分類別(2)]]
[[部分類別(3)]]

![[Pasted image 20241103171429.png]]
#### Required屬性：
[不可為 Null 的參考型別和 Required 屬性](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#non-nullable-reference-types-and-required-attribute)

假設專案設定為`<Nullable>enable</Nullable>`，以下方的`class dto`為範例，接收到的`FromBody(JSON)`或 `FormData`：
```C#
public class Person
{
	//string沒加?，代表一定要有`Name屬性值`
    public string Name { get; set; }
    
    //string有加?，不一定要有Name參數
    public string? Name { get; set; }
    
    //另外如果是實值類型的資料型態，加上`Required`是沒用的，前端沒回傳的話，系統會自帶預設值
    public int Age { get; set; }
    
    //承上點，如果要讓`Required`有作用的話，那就要標上?
    public int? Age { get; set; }
}
```

#### Remote屬性：
>Remote屬性會實作用戶端驗證，該驗證需要呼叫伺服器上的方法來判斷欄位輸入是否有效。 例如，應用程式可能需要驗證使用者名稱是否已經在使用中。

[Remote屬性](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#remote-attribute)
[可組合多個資料欄位驗證](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#additional-fields)
[[自訂模型資料驗證標籤ValidationAttribute]]
[[Model內自訂屬性資料驗證IValidatableObject]]

#### 前端驗證方式：
[用戶端驗證](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#client-side-validation)
[使用jQuery.validate.unobtrusive.js-黑暗執行緒](https://blog.darkthread.net/blog/unobtrusive-jquery-validation/)
[自訂用戶端驗證](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#custom-client-side-validation)

##### 後續動態產生的表單(Form) 或 控制項(input、select等等)，需重新呼叫以下API：
[將驗證新增至動態表單](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#add-validation-to-dynamic-forms)
```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

[將驗證新增至動態控制項](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/validation?view=aspnetcore-8.0#add-validation-to-dynamic-controls)
前端重新驗證表單資料(JQuery)，請移除先前剖析表單時新增的驗證資料，如下列範例所示：
```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```



