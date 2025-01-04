```csharp
[HttpPost]
public String Get([FromForm] Person p)
{
     return p.ID + p.Name;
}
 
public class Person
{
   public int ID { get; set; }
　 public string Name { get; set; }
}
```

http请求：
contentType：application/x-www-form-urlencoded
> 等同於 From HTML標籤
```html
<button class="add">提交</button>
<script src="~/lib/jquery/dist/jquery.js"></script>
<script>
    $(function ()
    {
        $('.add').bind('click', function ()
        {
            $.ajax({
                url: '/api/Default',
                contentType: "application/x-www-form-urlencoded",
                data: 'ID=15&Name=zhangsan',
                dataType: 'text',
                type: 'POST',
                success: function (data) {
                    console.log(data);
                }
            });
         });
    });
</script>
```
