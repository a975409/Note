```C#
public class ApiLoggingMiddleware
{
	private readonly RequestDelegate _next;

	public ApiLoggingMiddleware(RequestDelegate next)
	{
		_next = next;
	}

	public async Task InvokeAsync(HttpContext context, ProShiftDBContext dbcontext)
	{
		try
		{
			var request = context.Request;
			
			// 1. 取得完整網址
			var fullUrl = $"{request.Scheme}://{request.Host}{request.Path}{request.QueryString}";

			// 2. 取得 QueryString
			var queryString = request.QueryString.HasValue ? request.QueryString.Value : "";

			string requestData = "";

			// 3. 取得 Request Data（Form 或 Body）
			if (request.HasFormContentType)
			{
				// 讀取 Request FormData
				var form = await request.ReadFormAsync();
				requestData = string.Join("&", form.Select(kvp => $"{kvp.Key}={kvp.Value}"));
			}
			else 
			{
				// 讀取 Request Body
				request.EnableBuffering();
				
				using (var reader = new StreamReader(request.Body, Encoding.UTF8, leaveOpen: true))
				{
					requestData = await reader.ReadToEndAsync();
					request.Body.Position = 0;
				}
			}

			// 讀取 IP
			var ip = context.Connection.RemoteIpAddress?.ToString();

			// 攔截 Response Body
			var originalBodyStream = context.Response.Body;
			using var responseBody = new MemoryStream();
			context.Response.Body = responseBody;

			// 執行下一個 Middleware
			await _next(context);

			// 讀取 Response Body
			context.Response.Body.Seek(0, SeekOrigin.Begin);
			var responseBodyText = await new StreamReader(context.Response.Body).ReadToEndAsync();
			context.Response.Body.Seek(0, SeekOrigin.Begin);

			// 回寫 Response Body
			await responseBody.CopyToAsync(originalBodyStream);

			// 取得狀態碼
			var statusCode = (byte)context.Response.StatusCode;

			// 取得當下時間 (字串)
			var createTime = DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss");

			// 儲存到資料庫

			var log = new LogApi
			{
				ApiType = fullUrl,
				RequestData = requestData,
				ResponseData = responseBodyText,
				Ip = ip,
				Status = statusCode,
				CreateTime = createTime
			};

			dbcontext.LogApis.Add(log);
			await dbcontext.SaveChangesAsync();
		}
		catch (Exception ex)
		{
			Debug.WriteLine(ex);

			// 發生錯誤時，仍然呼叫下一個 Middleware
			await _next(context);
		}
	}
}

public static class ApiLoggingMiddlewareExtensions
{
	public static IApplicationBuilder UseRequestLogApi(
		this IApplicationBuilder builder)
	{
		return builder.UseMiddleware<ApiLoggingMiddleware>();
	}
}
```
