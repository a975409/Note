設定資料庫連線：
![[Pasted image 20250912111226.png]]

(LINQPad)主程式語法如下：
```C#
void Main()
{
	string query="";
	
	this.Connection.DumpClass(query,"violation").Dump();
}
```

以下程式碼貼到 void Main() 主程式下方：
```C#
public static class LINQPadExtensions
{
    private static readonly Dictionary<Type, string> TypeAliases = new Dictionary<Type, string> {
        { typeof(int), "int" },
        { typeof(short), "short" },
        { typeof(byte), "byte" },
        { typeof(byte[]), "byte[]" },
        { typeof(long), "long" },
        { typeof(double), "double" },
        { typeof(decimal), "decimal" },
        { typeof(float), "float" },
        { typeof(bool), "bool" },
        { typeof(string), "string" }
    };
     
    private static readonly HashSet<Type> NullableTypes = new HashSet<Type> {
        typeof(int),
        typeof(short),
        typeof(long),
        typeof(double),
        typeof(decimal),
        typeof(float),
        typeof(bool),
        typeof(DateTime)
    };
 
    public static string DumpClass(this IDbConnection connection, string sql, string className = "Info")
    {
        if(connection.State != ConnectionState.Open)
        {
				   connection.Open();
			  }
             
        var cmd = connection.CreateCommand();
        cmd.CommandText = sql;
        var reader = cmd.ExecuteReader();
                         
        var builder = new StringBuilder();
        do
        {
            if(reader.FieldCount <= 1) continue;
         
            builder.AppendFormat("public class {0}{1}", className, Environment.NewLine);
            builder.AppendLine("{");
            var schema = reader.GetSchemaTable();
                         
            foreach (DataRow row in schema.Rows)
            {
                var type = (Type)row["DataType"];
                var name = TypeAliases.ContainsKey(type) ? TypeAliases[type] : type.Name;
                var isNullable = (bool)row["AllowDBNull"] && NullableTypes.Contains(type);
                var collumnName = (string)row["ColumnName"];
                 
                builder.AppendLine(string.Format("\tpublic {0}{1} {2} {{ get; set; }}", name, isNullable ? "?" : string.Empty, collumnName));
    builder.AppendLine();
            }
             
            builder.AppendLine("}");
            builder.AppendLine();            
        } while(reader.NextResult());
         
        return builder.ToString();
    }
}
```

參考資料：
[Dapper - 使用 LINQPad 快速產生相對映 SQL Command 查詢結果的類別 | mrkt的程式學習筆記 - 點部落](https://dotblogs.com.tw/mrkt/2016/06/14/190618)

[SQL Server 資料類型對應 - ADO.NET](https://learn.microsoft.com/zh-tw/dotnet/framework/data/adonet/sql-server-data-type-mappings)
