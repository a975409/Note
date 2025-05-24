> gRPC傳輸的資料以Proto Buffers協定進行序列化，Proto Buffers 以高校緊湊的二進位編碼

> Proto 僅用來定義資料類型和服務，具體服務的實作內容需由Server負責實作

### 安裝Grpc.AspNetCore套件

### 建立*.proto檔，定義資料類型和服務

```csharp
syntax="proto3";
option csharp_namespace="App";

service Calculator{
	rpc Add(InputMessage)returns(OutputMessage);
	rpc Substract(InputMessage)returns(OutputMessage);
	rpc Multiply(InputMessage)returns(OutputMessage);
	rpc Divide(InputMessage)returns(OutputMessage);
}

message InputMessage{
	int32 x=1;
	int32 y=2;
}

message OutputMessage{
	int32 status=1;
	int32 result=2;
	string error=3;
}

```

> InputMessage、OutputMessage 均被定義為Proto Buffers訊息，底下的每個資料成員會被賦予唯一的序號，此序號會代替對應的成員名稱，寫入序列化的二進位內容中

### .proto檔案屬性調整為如下，之後就建置專案即可
![[Pasted image 20250524102145.png]]