#### View
區段(section)：
一般來說主頁面`Layout.cshtml`會有以下指令：
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - WebApplication1</title>
</head>
<body>
    //會有該指令
    @RenderSection("Scripts", required: false)
</body>
</html>
```

在其他`Razor Pages 檢視`，如果Layout都是指定為`Layout.cshtml`，則可將`Razor Pages` 檢視內的javascript或css之類的語法放到`區段(section)`內：
```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

然後就會呈現在`Layout.cshtml`內該區段所在的位置

#### 部分檢視(partialView)
參考資料：
https://learn.microsoft.com/zh-tw/aspnet/core/mvc/views/partial?view=aspnetcore-9.0#asynchronous-html-helper

非同步載入：
```HTML
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

將資料傳遞至部份檢視(partialView)：
```html
@await Html.PartialAsync("_PartialName", customViewData)
```

##### 以下為將資料傳遞至部份檢視(partialView)的完整範例：

`Views/Articles/Read.cshtml`
```html
@model PartialViewsSample.ViewModels.Article

<h2>@Model.Title</h2>
@* Pass the author's name to Views\Shared\_AuthorPartial.cshtml *@
@await Html.PartialAsync("_AuthorPartial", Model.AuthorName)
@Model.PublicationDate

@* Loop over the Sections and pass in a section and additional ViewData to 
   the strongly typed Views\Articles\_ArticleSection.cshtml partial view. *@
@{
    var index = 0;

    foreach (var section in Model.Sections)
    {
        @(await Html.PartialAsync("_ArticleSection", 
                                section,
                                new ViewDataDictionary(ViewData)
                                {
                                    { "index", index }
                                }))

        index++;
    }
}
```

> ViewDataDictionary等同於ViewData、ViewBag

`Views/Shared/_AuthorPartial.cshtml`
```html
@model string
<div>
    <h3>@Model</h3>
    This partial view from /Views/Shared/_AuthorPartial.cshtml.
</div>
```

`Views/Articles/_ArticleSection.cshtml`
```html
@using PartialViewsSample.ViewModels
@model ArticleSection

<h3>@Model.Title Index: @ViewData["index"]</h3>
<div>
    @Model.Content
</div>
```
