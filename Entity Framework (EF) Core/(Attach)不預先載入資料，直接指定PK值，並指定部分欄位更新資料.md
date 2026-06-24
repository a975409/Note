
範例如下：
```C#
public async Task<int> TransferOnSiteImagesToTemporaryAreaAsync(OnSiteImagesTransferToTemporaryAreaDto dto)
{
    foreach (var id in dto.Ids)
    {
	    // ✅ Attach 空殼，設定PK欄位
        var stub = new t_OnSiteVideo { Id = id };
        _context.t_OnSiteVideo.Attach(stub);

        stub.EventId = null;
        stub.AccidentId = null;

		//只標記 EventId / AccidentId 為修改
        _context.Entry(stub).Property(x=>x.EventId).IsModified= true;
        _context.Entry(stub).Property(x => x.AccidentId).IsModified = true;
    }

    return await _context.SaveChangesAsync();
}
```

# Id 為 `int` / `long`（數值型別） 且資料庫自動遞增（Auto Increment）

## 直接回答

> **不帶入 Id 值時（Id = `0L`），EF Core 會將其視為「新增」，執行 `INSERT`，並由資料庫自動產生遞增的 Id 值。**

## 原因說明

```csharp
// 不帶入 Id，long 預設值為 0
var stub = new t_OnSiteVideo { }; // Id = 0L
_context.t_OnSiteVideo.Attach(stub);
```

EF Core 的判斷邏輯：

```
long 型別 PK + 資料庫自動遞增
        ↓
Id == 0L  →  EF Core 認定「尚未有 PK 值」
        ↓
Attach() 將狀態設為 Added
        ↓
SaveChangesAsync() 執行 INSERT
        ↓
資料庫自動產生遞增 Id（例如：1, 2, 3...）
        ↓
EF Core 將產生的 Id 回填到 stub.Id
```

## 實際行為範例

```csharp
// ❌ 危險情境：沒有帶入 Id
var stub = new t_OnSiteVideo
{
    // Id 沒有設定，預設 = 0L
    EventId = null,
    AccidentId = null
};

_context.t_OnSiteVideo.Attach(stub);
_context.Entry(stub).Property(x => x.EventId).IsModified = true;
_context.Entry(stub).Property(x => x.AccidentId).IsModified = true;

await _context.SaveChangesAsync();

// 結果：執行的是 INSERT，而非 UPDATE！
// INSERT INTO t_OnSiteVideo (EventId, AccidentId) VALUES (NULL, NULL)
// 並回傳資料庫自動產生的新 Id
Console.WriteLine(stub.Id); // 輸出：資料庫產生的新 Id，例如 1024
```

## ✅ 與帶入 Id 的行為對照

```csharp
// ✅ 正確：帶入實際 Id（例如 id = 5L）
var stub = new t_OnSiteVideo { Id = 5L };
_context.t_OnSiteVideo.Attach(stub);
stub.EventId = null;
stub.AccidentId = null;
_context.Entry(stub).Property(x => x.EventId).IsModified = true;
_context.Entry(stub).Property(x => x.AccidentId).IsModified = true;
await _context.SaveChangesAsync();
// 執行：UPDATE t_OnSiteVideo SET EventId = NULL, AccidentId = NULL WHERE Id = 5
```

| 情況      | Id 值      | EF Core 狀態               | SQL 執行                |
| ------- | --------- | ------------------------ | --------------------- |
| 帶入實際 Id | `5L`      | `Unchanged` → `Modified` | `UPDATE WHERE Id = 5` |
| 未帶入 Id  | `0L`（預設值） | `Added`                  | `INSERT`（新增一筆）        |

# 標記資料刪除
```C#
var stub = new t_OnSiteVideo { Id = id }; // 只需要 PK 值

// 方法一、用 Attach 標記刪除（不需要先查詢資料庫）
_context.t_OnSiteVideo.Attach(stub);
_context.t_OnSiteVideo.Remove(stub);      // 標記為 Deleted

// 方法二、更簡潔的寫法：直接用 `Entry` 設定狀態
_context.Entry(stub).State = EntityState.Deleted;

await _context.SaveChangesAsync();
// 執行：DELETE FROM t_OnSiteVideo WHERE Id = {id}
```
## ⚠️ 注意事項

> **若該 Id 在資料庫不存在**，執行 `DELETE` 時 EF Core **不會拋出例外**，只是影響 0 筆資料
