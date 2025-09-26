[何謂模型繫結](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/model-binding?view=aspnetcore-8.0#what-is-model-binding)
[模型繫結簡單和複雜類型](https://learn.microsoft.com/zh-tw/aspnet/core/mvc/models/model-binding?view=aspnetcore-8.0#model-binding-simple-and-complex-types)
[[自訂模型繫結]]


![[Pasted image 20241103161208.png]]

[[FromBody]]
[[FromFrom]]
#### JsonIgnore屬性：
>忽略序列化屬性，主要用在反向導覽屬性上，用來解決`JSON`序列化循環參考的問題
```C#
public partial class User
{
    [Key, DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    public int UserId { get; set; }

    [Display(Name = "電子信箱")]
    public string Email { get; set; }

    [Display(Name = "密碼")]
    public string Password { get; set; }

    [JsonIgnore]
    [BindNever]
    public virtual Role Role { get; set; }
}
```