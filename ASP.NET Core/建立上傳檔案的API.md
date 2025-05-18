
#### 後端程式碼：
> 單一檔案：`IFormFile`   
   複數檔案：`IFormFileCollection`、`IEnumerable<IFormFile>`、`List<IFormFile>`、`ICollection<IFormFile>`

```csharp
[HttpPost("api/Register")]
public IActionResult Register([FromForm] RegisterUser registerUser, [UploadFileValid] IFormFile? picfile)
{
	var user = new User
	{
		Email = registerUser.Email,
		Password = registerUser.Password.ToMD5(),
		PasswordConfirmed = registerUser.PasswordConfirmed,
		UserName = registerUser.UserName,
		Name = registerUser.Name,
		Birthday = registerUser.Birthday,
		PhoneNumber = registerUser.PhoneNumber
	};

	if (picfile != null && picfile.Length > 0)
	{
		using (var ms = new MemoryStream())
		{
			picfile.CopyTo(ms);
			user.Picture = ms.ToArray();
		}
	}

	user.RoleId = 2;
	_context.Users.Add(user);

	try
	{
		_context.SaveChanges();
		return Ok(user.UserId);
	}
	catch (Exception ex)
	{
		return StatusCode(500, ex.Message);
	}
}

```

後端開發注意如下：
1. 如果API會接受上傳檔案和其他參數，記得參數的部分要標記FromForm
2. 參考資料：[msdn](https://docs.microsoft.com/zh-tw/aspnet/core/mvc/models/file-uploads?view=aspnetcore-6.0#file-upload-scenarios)、[# 415 unsupported media [type错误解决.net](http://xn--type-y38f2980a18ch6y.net/) core API]

#### 前端HTML `<form>`標籤
首先先在View中加入上傳檔案的input
> 如果要上傳多個檔案，就要在`input type="file"`標記 multiple
```html
<div class="form-group">
    <label for="Files" class="control-label">Files</label>
    <input name="Files" type="file" multiple class="form-control" />
</div>
```

另外上面的from要多加一段宣告`enctype="multipart/form-data"`，才能夠接收到檔案
```html
<form method="post" action="/News2/Create" enctype="multipart/form-data">
```

#### 前端呼叫API (axios & Vue)：
> 記得在header標記 "Content-Type": "multipart/form-data"
```jsx
var RegisterApp = new Vue({
	el: '#Register',
	data: {
		form: {
			Email: '',
			Password: '',
			PasswordConfirmed: '',
			UserName: '',
			Name: '',
			Birthday: '',
			PhoneNumber: '',
			picfile:''
		}
	},
	methods: {
		RegisterPost: function() {

			for (let key in this.error) {
				this.error[key] = '';
			}

			let config = {
				headers: {
					"Content-Type": "multipart/form-data"
				}
			};

			axios.post('/Login/api/Register', this.form, config)
				.then((response) => {
					location.href = '/Login/Index';
				})
				.catch((error) => {
					if (error.response.status === 400) {
						let errorObj = error.response.data.errors;

						for (let key in errorObj) {
							this.error[key] = errorObj[key].join('\\\\n');
						}
					}
				});
		},

		//input type="file" 檔案選擇後會觸發的事件
		//<input class="d-none" name="picfile" id="picfile" type="file" v-on:change="fileChange">
		fileChange: function(e) {
			//https://www.dotblogs.com.tw/kinanson/2017/06/07/092521
			this.form.picfile = e.target.files[0];

			let objUrl = URL.createObjectURL(e.target.files[0]);
			document.getElementById('userAvatarImg').src = objUrl;
		}
	}
});

```

參考文件 [https://docs.microsoft.com/zh-tw/aspnet/core/mvc/models/file-uploads?view=aspnetcore-6.0#file-upload-scenarios](https://docs.microsoft.com/zh-tw/aspnet/core/mvc/models/file-uploads?view=aspnetcore-6.0#file-upload-scenarios)