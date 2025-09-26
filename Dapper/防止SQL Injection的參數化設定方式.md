第一種：

```csharp
/// <summary>
/// 查詢卡片
/// </summary>
/// <returns></returns>
public Card Get(int id)
{
    var sql =
    @"
        SELECT * 
        FROM Card 
        Where Id = @id
    ";

    var parameters = new DynamicParameters();
    parameters.Add("@id", id);

    using (var conn = new SqlConnection(_connectString))
    {
        var result = conn.QueryFirstOrDefault<Card>(sql, parameters);
        return result;
    }
}
```

第二種(where in)：

```csharp
var ids = new[] { 3, 7, 12 };
var sql = "SELECT * FROM Products WHERE ProductId IN @Ids;";
using (var connection = new SqlConnection(connectionString))
{
	connection.Open();
	var products = connection.Query<Product>(sql, new {Ids = ids }).ToList();
}
```

第三種(where in)：

```csharp
var ids = new[] { 3, 7, 12 };
var sql = "SELECT * FROM Products WHERE ProductId IN @Ids;";

var dictionary = new Dictionary<string, object>
{
    { "@Ids", ids  }
};
var parameters = new DynamicParameters(dictionary);

using (var connection = new SqlConnection(connectionString))
{
	connection.Open();
	var products = connection.Query<Product>(sql, parameters).ToList();
}
```