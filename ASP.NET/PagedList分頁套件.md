ASP.NET：
> 安裝`PagedList.Mvc`  

ASP.NET Core：
> 安裝`X.PagedList.Mvc.Core`  

![https://ithelp.ithome.com.tw/upload/images/20210922/201400010L8tDfnoBX.png](https://ithelp.ithome.com.tw/upload/images/20210922/201400010L8tDfnoBX.png)

`BlogsController`
```C#
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Entity;
using System.Linq;
using System.Net;
using System.Web;
using System.Web.Mvc;
using CodeFirstSln.App_Start;
using CodeFirstSln.Models;
using PagedList;

namespace CodeFirstSln.Controllers
{
    public class BlogsController : Controller
    {
        private BloggingContext db = new BloggingContext();

        // GET: Blogs
        public ActionResult Index(int page = 1, int pageSize = 1)
        {
            //會以SQL指令進行分頁查詢
            //呼叫ToPagedList前，一定要設定OrderBy，不然會執行失敗
            var result = db.Blogs.OrderBy(m=>m.BlogId).ToPagedList(page, pageSize);
            return View(result);
        }
    }
}
```

`Blogs\Index.cshtml`
```html
@model IEnumerable<CodeFirstSln.Models.Blog>

<!--新增以下-->
@using PagedList.Mvc;
@using PagedList;

@{
    ViewBag.Title = "Index";
}

<h2>Index</h2>

<p>
    @Html.ActionLink("Create New", "Create")
</p>
<table class="table">
    <tr>
        <th>
            @Html.DisplayNameFor(model => model.Name)
        </th>
        <th></th>
    </tr>

    @foreach (var item in Model)
    {
        <tr>
            <td>
                @Html.DisplayFor(modelItem => item.Name)
            </td>
            <td>
                @Html.ActionLink("Edit", "Edit", new { id = item.BlogId }) |
                @Html.ActionLink("Details", "Details", new { id = item.BlogId }) |
                @Html.ActionLink("Delete", "Delete", new { id = item.BlogId })
            </td>
        </tr>
    }

</table>

<!--新增這段-->
@Html.PagedListPager((IPagedList)Model, x => Url.Action("Index", new { page = x }))

```
