> 模擬用戶端呼叫gRPC服務

```csharp
internal class Program
{
	static async Task Main(string[] args)
	{
		//Server端
		using var channel = GrpcChannel.ForAddress("<https://localhost:7214/>");
		var client = new Calculator.CalculatorClient(channel);

		var inputMessage = new InputMessage { X = 1, Y = 0 };

		//以下為呼叫gRPC服務
		await InvokeAsync(input => client.AddAsync(input), inputMessage, "+");
		await InvokeAsync(input => client.SubstractAsync(input), inputMessage, "-");
		await InvokeAsync(input => client.MultiplyAsync(input), inputMessage, "*");
		await InvokeAsync(input => client.DivideAsync(input), inputMessage, "/");

		Console.ReadKey();
	}

	static async Task InvokeAsync(Func<InputMessage, AsyncUnaryCall<OutputMessage>> invoker, InputMessage input, string @operator)
	{
		var output = await invoker(input);

		if (output.Status == 0)
		{
			Console.WriteLine($"{input.X}{@operator}{input.Y}={output.Result}");
		}
		else
		{
			Console.WriteLine(output.Error);
		}
	}
}

```