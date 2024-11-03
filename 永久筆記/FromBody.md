Model：

```csharp
public class Test
   {
       public int id { get; set; }
 
       public String Name { get; set; }
 
       public List<String> images { get; set; }
 
       public aaa a1 { get; set; }
   }
 
 
   public class aaa
   {
       public int Age { get; set; }
       public String Address { get; set; }
   }
```

Controller:

```csharp
[HttpPost]
public void Get([FromBody]Test test)
{
            
}
```

http请求:contentType:application/json;
```JSON
raw:
{
	id:1, 　　
	Name:'呵呵', 　　
	images:["aaaa","vvvvv","cccc"], 　　
	a1:{Age:18,Address:'127.0.0.1'} 
}
```