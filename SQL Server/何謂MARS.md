簡單來說，**MARS（Multiple Active Result Sets，多重活動結果集）** 允許你在**同一個資料庫連線（Connection）中，同時執行多個查詢，且不需要等待前一個查詢完全結束。**

以下用一個常見的 C# 範例來展示「有 MARS」與「沒有 MARS」的差別。

### 情境：讀取訂單的同時，去查詢並更新客戶資料

假設我們有一段程式碼：先查詢所有的「訂單（Orders）」，然後用 `foreach` 逐筆讀取訂單，在讀取的過程中，同時去資料庫查詢該訂單的「客戶（Customer）」詳細資料。

```csharp
// 1. 開啟連線
using (var connection = new SqlConnection(connectionString))
{
    connection.Open();

    // 2. 執行第一個查詢：獲取所有訂單
    var orderCommand = new SqlCommand("SELECT OrderId, CustomerId FROM Orders", connection);
    using (SqlDataReader reader = orderCommand.ExecuteReader())
    {
        while (reader.Read()) // <--- 這裡 DataReader 還在讀取，連線處於「忙碌」狀態
        {
            var customerId = reader["CustomerId"];

            // 3. 在同一個連線上，執行第二個查詢：獲取客戶名稱
            var customerCommand = new SqlCommand(
                $"SELECT Name FROM Customers WHERE Id = {customerId}", connection);
            
            // 這裡會發生什麼事？
            string customerName = (string)customerCommand.ExecuteScalar(); 
            
            Console.WriteLine($"Order ID: {reader["OrderId"]}, Customer: {customerName}");
        }
    }
}
```

---

### 差別一：如果「禁用」MARS（`MultipleActiveResultSets=False`）

當程式執行到 `customerCommand.ExecuteScalar()` 時，系統會**立刻崩潰**，並拋出以下錯誤：

> **InvalidOperationException**: "There is already an open DataReader associated with this Command which must be closed first."
> *(譯：此 Command 已經有一個關聯的開啟中 DataReader，必須先將其關閉。)*

*   **原因**：因為沒有啟用 MARS，一個連線在同一時間只能做一件事。當 `reader` 還在讀取訂單時，該連線就被鎖定了，你不能用同一個連線去執行第二個查詢（查詢客戶）。

---

### 差別二：如果「啟用」MARS（`MultipleActiveResultSets=True`）

程式會**完美流暢地執行完畢**，不會報錯。

*   **原因**：SQL Server 允許在同一個連線上，一邊保持 `reader` 讀取訂單，一邊讓 `customerCommand` 去查詢客戶。

---

### 為什麼大家常說「盡量不要用 MARS」？

雖然 MARS 看起來很方便，但它有代價：
1.  **資源消耗大**：SQL Server 必須在記憶體中幫你維護多個查詢的狀態，這會消耗更多伺服器 CPU 和記憶體（容易導致你遇到的 8642 執行緒不足錯誤）。
2.  **有更好的替代寫法**：
    在沒有 MARS 的情況下，我們只需要把資料先讀進記憶體（例如 EF Core 使用 `.ToList()`），釋放連線後，再做後續處理即可：
    ```csharp
    // 更好的寫法：先用 ToList() 把訂單一次查出來（關閉 Reader 釋放連線）
    var orders = dbContext.Orders.ToList(); 

    foreach(var order in orders)
    {
        // 此時連線已經空閒，可以安全地查詢客戶，不需要 MARS
        var customer = dbContext.Customers.Find(order.CustomerId); 
    }
    ```


### 那在什麼樣的時機點會需要使用MARS?
在現代的 C# 與 EF Core 開發中，**絕大多數情況下都不需要 MARS**，而且通常可以透過更好的架構設計（例如 `JOIN` 查詢、`.ToList()` 批次載入）來避免使用它。

不過，確實有一些**特定的時機點或情境**，使用 MARS 會是最簡單、最直覺，甚至是唯一的解決方案：

### 1. 執行「交錯的」非同步操作（Interleaved Asynchronous Operations）
當你的應用程式在同一個 HTTP 請求或同一個 `DbContext` 實例中，需要**同時（並行）發送多個非同步資料庫請求**，且這些請求共用同一個連線時。

*   **範例**：
    在一個 Web API 控制器中，你希望同時去資料庫撈取「最新公告」和「使用者個人資料」，並使用 `Task.WhenAll` 來加速：
    ```csharp
    // 兩個查詢同時並行發送
    var bannersTask = _context.Banners.ToListAsync();
    var profileTask = _context.UserProfile.FirstOrDefaultAsync(u => u.Id == userId);

    // 等待兩者完成
    await Task.WhenAll(bannersTask, profileTask);
    ```
    *   **如果沒有 MARS**：這會直接報錯，因為兩個非同步工作在爭奪同一個連線。
    *   **如果有 MARS**：這段程式碼可以正常運作。
    *   *(註：雖然這可以用兩個不同的 DbContext 實例或 Scoped 連線來解決，但啟用 MARS 是最省事的方法。)*

---

### 2. 無法一次性載入記憶體的大數據流處理（Streaming Large Data）
當你需要處理**幾百萬筆資料**，且必須對每筆資料進行複雜的逐筆運算（甚至寫回資料庫）時。

*   **為什麼不能用 `.ToList()`？**
    因為幾百萬筆資料一次 `.ToList()` 載入記憶體，會直接導致伺服器 **OOM (Out of Memory，記憶體溢出)**。
*   **MARS 的派上用場之處**：
    你需要使用 `IEnumerable` 或 `IQueryable` 保持與資料庫的連線，以「串流（Streaming）」的方式每次只讀取一筆，並在讀取的過程中，同時去查詢其他關聯資料表或寫入進度。
    ```csharp
    // 串流讀取 1000 萬筆歷史紀錄（不佔用記憶體）
    var largeLogs = _context.Logs.AsNoTracking().ToIEnumerable(); 

    foreach (var log in largeLogs)
    {
        // 讀取的同時，必須去查詢另一個表來驗證資料
        var isValid = _context.Users.Any(u => u.Id == log.UserId); // 需要 MARS
        
        if (!isValid) { /* 處理異常 */ }
    }
    ```

---

### 3. 遺留系統（Legacy Code）重構困難
在一些歷史悠久、規模龐大的舊專案中，前人可能寫了大量的「巢狀迴圈資料庫查詢」（如 `foreach` 裡面再包 `SQL 查詢`）。
*   如果要徹底移除 MARS，工程師必須重構數百個 API，將它們改成 `JOIN` 或批次查詢，這會帶來極大的測試成本與風險。
*   此時，**保持 MARS 啟用**是維持系統正常運作、避免重構災難的妥協方案。

---

### 4. 使用 EF Core 的「拆分查詢（Split Queries）」
當你使用 EF Core 載入含有多個「一對多」關係（Collection Navigation Properties）的複雜實體時，如果使用單一查詢（Single Query），SQL Server 會產生巨大的 `LEFT JOIN`，導致「笛卡兒積（Cartesian Explosion）」問題，讓傳輸的資料量暴增、效能極差。

EF Core 引入了 **Split Queries** 功能，會將一個 LINQ 查詢拆分成多條 SQL 語句分開執行。

```csharp
// EF Core 會拆分成多個 SQL 語句發送
var blog = context.Blogs
    .AsSplitQuery() 
    .Include(b => b.Posts)
    .Include(b => b.Contributors)
    .FirstOrDefault();
```
雖然 EF Core 在執行拆分查詢時，會儘量在背後協調連線，但在某些複雜交易情境下，**啟用 MARS 可以確保拆分查詢在同一個連線與交易中更平滑地執行**。

---

### 總結：你該怎麼選擇？

*   **新專案 / 效能要求高的系統**：**不建議啟用 MARS**。應該透過寫好 SQL `JOIN`、使用 DTO 投影（Projection）、或是先 `.ToList()` 載入記憶體來解決多重查詢問題。這樣可以讓 SQL Server 負載更低、更穩定。
*   **需要並行非同步查詢 / 處理超大數據串流**：可以啟用 MARS，但必須注意監控 SQL Server 的 CPU 與執行緒狀況，並避免在這些連線中進行複雜的交易（Transaction）操作，以防遇到 Savepoints 禁用或執行緒耗盡的問題。


### 30004 警告訊息

訊息內容：
``` text
Category: Microsoft.EntityFrameworkCore.Database.Transaction
EventId: 30004

Savepoints are disabled because Multiple Active Result Sets (MARS) is enabled. If 'SaveChanges' fails, then the transaction cannot be automatically rolled back to a known clean state. Instead, the transaction should be rolled back by the application before retrying 'SaveChanges'. See https://go.microsoft.com/fwlink/?linkid=2149338 for more information and examples. To identify the code which triggers this warning, call 'ConfigureWarnings(w => w.Throw(SqlServerEventId.SavepointsDisabledBecauseOfMARS))'.
```

這個警告訊息（EventId: 30004）是 Entity Framework Core (EF Core) 在提醒你：**因為你啟用了 MARS (Multiple Active Result Sets)，所以 EF Core 自動禁用了「儲存點 (Savepoints)」功能。**

這與你前一個提到的 `SqlException (0x80131904)` 執行緒不足錯誤**有非常高的關聯性**。

以下為您詳細解析這個警告的原因、潛在風險以及如何解決：

---

### 1. 為什麼會出現這個警告？

*   **什麼是 MARS？**
    MARS 允許在單一的 SQL 連線中，同時執行多個查詢或指令（例如：在讀取 `DataReader` 的同時，又發送寫入指令）。你在資料庫連線字串（Connection String）中一定有設定 `MultipleActiveResultSets=True`。
*   **什麼是 Savepoints（儲存點）？**
    當你在 EF Core 中呼叫 `SaveChanges()` 且它已經處於一個交易（Transaction）中時，EF Core 預設會建立一個「儲存點」。如果這次 `SaveChanges()` 失敗，EF Core 可以只復原（Rollback）到這個儲存點，而不用放棄整個大交易。
*   **衝突點：**
    SQL Server 不支援**同時**使用 MARS 和 Savepoints。當 MARS 啟用時，SQL Server 會拒絕建立 Savepoints。因此，EF Core 只能默默地將 Savepoints 功能關閉，並噴出這個警告。

---

### 2. 這會帶來什麼風險？

如果你的程式碼中，在同一個交易內多次呼叫 `SaveChanges()`：
1. 第一次 `SaveChanges()` 成功。
2. 第二次 `SaveChanges()` 失敗（例如因為併發衝突、欄位長度超出限制、或你剛剛遇到的執行緒不足錯誤）。
3. **後果**：因為沒有 Savepoints，EF Core **無法自動**幫你復原到第二次執行前的狀態。你的交易可能會處於一個「髒狀態（Dirty State）」，如果直接重試，可能會導致資料不一致或重複寫入。

---

### 3. 如何解決？

要解決這個警告，有以下幾種常見的策略：

#### 方案 A：關閉 MARS（最推薦）
**絕大多數專案其實不需要 MARS**。MARS 雖然方便，但它會消耗更多伺服器資源（如記憶體和連線執行緒），這可能也是導致你前一個「平行查詢執行緒不足 (8642)」錯誤的誘因之一。

*   **做法**：檢查你的 `appsettings.json` 中的資料庫連線字串，將 `MultipleActiveResultSets=True` 移除，或者明確設為 `False`。
    ```json
    "ConnectionStrings": {
      "DefaultConnection": "Server=...;Database=...;User Id=...;Password=...;MultipleActiveResultSets=False;TrustServerCertificate=True;"
    }
    ```
*   *注意*：關閉 MARS 後，如果你的程式碼中有「在 `foreach` 巡覽查詢結果的同時，又在迴圈內查詢或修改資料庫」的情況，可能會報錯。這時應該將查詢改為 `.ToList()` 先載入記憶體，再進行後續操作（這也是更好的開發習慣）。

#### 方案 B：在 EF Core 中明確禁用 Savepoints（消除警告）
如果你評估後，確定專案**必須**使用 MARS，且你不在乎 Savepoints 的自動復原功能（或者你會在程式碼中自行處理 `try-catch` 與交易的 `Rollback()`），你可以配置 EF Core 忽略這個警告，或者明確告訴 EF Core 不要使用 Savepoints。

在你的 `DbContext` 的 `OnConfiguring` 中設定：
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseSqlServer("你的連線字串", options => 
    {
        // 禁用 Savepoints，這樣 EF Core 就不會嘗試建立，也不會噴警告
        options.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery);
    })
    .ConfigureWarnings(warnings => 
        // 或者直接忽略這個警告
        warnings.Ignore(SqlServerEventId.SavepointsDisabledBecauseOfMARS)
    );
}
```

#### 方案 C：找出是哪段程式碼觸發的
如果你想知道到底是哪裡在呼叫 `SaveChanges` 時觸發了這個警告，可以按照警告訊息的建議，在開發環境將該警告設定為**直接拋出異常**，這樣你就能從 Stack Trace 找到源頭：

```csharp
// 僅在開發環境啟用，方便 Debug
optionsBuilder.ConfigureWarnings(w => w.Throw(SqlServerEventId.SavepointsDisabledBecauseOfMARS));
```

### 總結建議
啟用 MARS 會讓 SQL Server 在背後維護多個動態的結果集，這會顯著增加 SQL Server 的 Session 負擔與執行緒消耗。關閉 MARS 不僅能解決這個警告，還能有效緩解 SQL Server 資源耗盡、無法啟動平行處理執行緒的問題。