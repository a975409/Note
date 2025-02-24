```csharp
/// <summary>
/// 針對由客戶端回傳的字串參數加上HtmlSanitizer做過濾，防止XSS攻擊
/// <https://josephjsf2.github.io/security/2019/09/19/cross-site-scripting.html>
/// </summary>
public class XSSFilter : ActionFilterAttribute
{
    private readonly HtmlSanitizer htmlSanitizer = new HtmlSanitizer();

    /// <summary>
    /// 處理 Stored XSS attacks 
    /// </summary>
    /// <param name="context"></param>
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        base.OnActionExecuting(context);

        var parameters = context.ActionArguments;

        if (parameters == null || parameters.Count == 0)
            return;

        foreach (var parameter in parameters)
        {
            var model = parameter.Value;

            if (model == null)
                continue;

            var list = new ArrayList();

            if (typeof(ICollection).IsAssignableFrom(model.GetType()))
            {
                list.AddRange(model as ICollection);
            }
            else
            {
                list.Add(model);
            }

            foreach (var item in list)
            {
                if (item == null)
                    continue;

                var propertys = item.GetType().GetProperties();

                foreach (var property in propertys)
                {
                    string propertyTypeName = property.PropertyType.Name.ToLower();

                    if (propertyTypeName.Contains("string") && property.GetValue(item) != null && property.GetSetMethod() != null)
                    {
                        //可查看該屬性輸出的型態名稱
                        //Debug.WriteLine($"propertyTypeName:{propertyTypeName}");

                        if (propertyTypeName.EndsWith("string"))
                        {
                            var value = HtmlXSSFilter(property.GetValue(item).ToString());
                            property.SetValue(item, value);
                        }
                        else if (propertyTypeName.EndsWith("string[]"))
                        {
                            string[] values = property.GetValue(item) as string[];

                            for (int i = 0; i < values.Length; i++)
                            {
                                values[i] = HtmlXSSFilter(values[i]);
                            }
                        }
                    }
                }
            }
        }
    }

    /// <summary>
    /// 處理 Reflected XSS attacks
    /// </summary>
    /// <param name="context"></param>
    public override void OnResultExecuted(ResultExecutedContext context)
    {
        var result = context.Result as ViewResult;

        if (result != null)
        {
            ProcessViewData(result.ViewData);
            if (result.ViewData.Model != null)
                ProcessObjectProperties(result.ViewData.Model);
        }

        base.OnResultExecuted(context);
    }

    /// <summary>
    /// 針對ViewBag、ViewData的值做處理
    /// </summary>
    /// <param name="viewData"></param>
    private void ProcessViewData(ViewDataDictionary viewData)
    {
        foreach (var key in viewData.Keys.ToList())
        {
            var value = viewData[key];

            if (value != null)
            {
                // 如果值是一个字符串，则对其进行 XSS 防护
                if (value is string stringValue)
                {
                    viewData[key] = HtmlXSSFilter(stringValue);
                }
                // 如果值是一个对象，则递归处理其属性
                else if (value.GetType().IsClass && !value.GetType().IsPrimitive)
                {
                    ProcessObjectProperties(value);
                }
            }
        }
    }

    /// <summary>
    /// 針對物件內所有屬性的值做處理
    /// </summary>
    /// <param name="obj"></param>
    private void ProcessObjectProperties(object obj)
    {
        var properties = obj.GetType().GetProperties(BindingFlags.Public | BindingFlags.Instance);

        if (obj.GetType().IsArray)
        {
            foreach (var item in (Array)obj)
                ProcessObjectProperties(item);

            return;
        }
        else if (obj is IEnumerable)
        {
            List<object> list = new List<object>();
            foreach (var item in (IEnumerable)obj)
                list.Add(item);

            obj = list;
            foreach (var item in list)
                ProcessObjectProperties(item);

            return;
        }
        else if (obj is ICollection)
        {
            foreach (var item in (ICollection)obj)
                ProcessObjectProperties(item);
        }
        else if (obj is IList)
        {
            foreach (var item in (IList)obj)
                ProcessObjectProperties(item);
        }
        else
        {
            foreach (var property in properties)
            {
                var value = property.GetValue(obj);

                if (value != null)
                {
                    // 如果属性值是一个字符串，则对其进行 XSS 防护
                    if (value is string stringValue)
                    {
                        property.SetValue(obj, HtmlXSSFilter(stringValue));
                    }
                    // 如果属性值是一个对象，则递归处理其属性
                    else if (value.GetType().IsClass && !value.GetType().IsPrimitive)
                    {
                        ProcessObjectProperties(value);
                    }
                }
            }
        }
    }

    /// <summary>
    /// 單純針對字串做處理
    /// </summary>
    /// <param name="html"></param>
    /// <returns></returns>
    private string HtmlXSSFilter(string html)
    {
        if (!string.IsNullOrWhiteSpace(html) && htmlSanitizer != null)
            html = htmlSanitizer.Sanitize(html);

        return html;
    }
}
```

然後在Program.cs全域註冊即可：

```csharp
builder.Services.AddControllersWithViews(option=>option.Filters.Add(ModelFillAttribute));
```

※2024/01/10 調整後版本：

```csharp
/// <summary>
/// 針對由客戶端回傳的字串參數加上HtmlSanitizer做過濾，防止XSS攻擊
/// <https://josephjsf2.github.io/security/2019/09/19/cross-site-scripting.html>
/// </summary>
public class XSSFilter : ActionFilterAttribute
{
    private readonly HtmlSanitizer htmlSanitizer = new HtmlSanitizer();

    /// <summary>
    /// 處理 Stored XSS attacks 
    /// </summary>
    /// <param name="context"></param>
    public override void OnActionExecuting(ActionExecutingContext context)
    {
        var parameters = context.ActionArguments;

        foreach (var key in parameters.Keys)
            parameters[key] = SanitizeActionParameters(parameters[key]);

        base.OnActionExecuting(context);
    }

    /// <summary>
    /// 處理 Reflected XSS attacks
    /// </summary>
    /// <param name="context"></param>
    public override void OnResultExecuted(ResultExecutedContext context)
    {
        var result = context.Result as ViewResult;

        if (result != null)
        {
            //針對ViewBag、ViewData的值做處理
            foreach (var key in result.ViewData.Keys.ToList())
            {
                if (result.ViewData[key] != null)
                {
                    result.ViewData[key] = SanitizeActionParameters(result.ViewData[key]);
                }
            }

            if (result.ViewData.Model != null)
                result.ViewData.Model = SanitizeActionParameters(result.ViewData.Model);
        }

        base.OnResultExecuted(context);
    }

    /// <summary>
    /// 依據value的型態決定處理編碼的方式
    /// </summary>
    /// <param name="value"></param>
    /// <returns></returns>
    private object SanitizeActionParameters(object value)
    {
        if (value == null)
            return value;

        if (value is string stringValue)
        {
            value = HtmlXSSFilter(stringValue);
        }
        else if (value.GetType().IsArray)
        {
            if (value is string[] stringArray)
            {
                for (int i = 0; i < stringArray.Length; i++)
                    stringArray[i] = SanitizeActionParameters(stringArray[i]) as string;
            }
        }
        else if (value is IEnumerable)
        {
            List<object> list = new List<object>();
            foreach (var item in (IEnumerable)value)
                list.Add(item);

            for (int i = 0; i < list.Count; i++)
                list[i] = SanitizeActionParameters(list[i]);

            value = list;
        }
        else if (value is ICollection)
        {
            List<object> list = new List<object>();
            foreach (var item in (ICollection)value)
                list.Add(item);

            for (int i = 0; i < list.Count; i++)
                list[i] = SanitizeActionParameters(list[i]);

            value = list;
        }
        else if (value is IList)
        {
            var list = value as IList;

            for (int i = 0; i < list.Count; i++)
                list[i] = SanitizeActionParameters(list[i]);
        }
        else if (value.GetType().IsClass && value.GetType().IsPrimitive == false)
            ProcessObjectProperties(value);

        return value;
    }

    /// <summary>
    /// 針對物件內所有屬性的值做處理
    /// </summary>
    /// <param name="obj"></param>
    private void ProcessObjectProperties(object obj)
    {
        if (obj.GetType().IsClass == false || obj.GetType().IsPrimitive|| obj == null)
            return;

        var properties = obj.GetType().GetProperties(BindingFlags.Public | BindingFlags.Instance);

        foreach (var property in properties)
        {
						var value = property.GetValue(obj);
						property.SetValue(obj, SanitizeActionParameters(value));
        }
    }

    /// <summary>
    /// 單純針對字串做處理
    /// </summary>
    /// <param name="html"></param>
    /// <returns></returns>
    private string HtmlXSSFilter(string html)
    {
        if (!string.IsNullOrWhiteSpace(html) && htmlSanitizer != null)
            html = htmlSanitizer.Sanitize(html);

        return html;
    }
}
```