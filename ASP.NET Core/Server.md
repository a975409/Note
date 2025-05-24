> 依照 Proto 定義資料類型和服務具體實作其內容

[[Proto]]
### 以下為實作內容，實作CalculatorService並繼承Calculator.CalculatorBase

```csharp
public class CalculatorService : Calculator.CalculatorBase
{
	private readonly ILogger _logger;
	public CalculatorService(ILogger logger)
	{
		_logger = logger;
	}

	public override Task<OutputMessage> Add(InputMessage request, ServerCallContext context)
	{
		return InvokeAsync((op1, op2) => op1 + op2, request);
	}

	public override Task<OutputMessage> Substract(InputMessage request, ServerCallContext context)
	{
		return InvokeAsync((op1, op2) => op1 - op2, request);
	}

	public override Task<OutputMessage> Multiply(InputMessage request, ServerCallContext context)
	{
		return InvokeAsync((op1, op2) => op1 * op2, request);
	}

	public override Task<OutputMessage> Divide(InputMessage request, ServerCallContext context)
	{
		return InvokeAsync((op1, op2) => op1 / op2, request);
	}

	private Task<OutputMessage> InvokeAsync(Func<int, int, int> calculate, InputMessage input)
	{
		OutputMessage output;
		try
		{
			output = new OutputMessage
			{
				Status = 0,
				Result = calculate(input.X, input.Y)
			};
		}
		catch (Exception ex)
		{

			_logger.LogError(ex, "Calculate error.");
			output = new OutputMessage { Status = 1, Error = ex.ToString() };
		}

		return Task.FromResult(output);
	}
}
```

> Calculator.CalculatorBase是由 .proto 檔定義並編譯後產生的.cs檔

### 實作CalculatorService後，需註冊gRPC服務

```csharp
public class Program
{
	public static void Main(string[] args)
	{
		var builder = WebApplication.CreateBuilder(args);

		//以下2段需新增
		builder.WebHost.ConfigureKestrel(kestrel => kestrel.ConfigureEndpointDefaults(endpoint => endpoint.Protocols = HttpProtocols.Http2));
		builder.Services.AddGrpc();

		var app = builder.Build();
		app.MapGrpcService<CalculatorService>(); //需註冊CalculatorService
		app.MapGet("/", () => "Hello World!");

		app.Run();
	}
}

```