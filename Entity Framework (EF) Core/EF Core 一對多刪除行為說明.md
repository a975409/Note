# EF Core 一對多刪除行為說明

依照您目前的程式碼設定，**答案是：會，預設會自動連帶刪除（Cascade Delete）**。

## 為什麼會這樣？

這是因為 EF Core 的**慣例（Convention）**機制，判斷依據是**外鍵是否可為 Null**：

```csharp
public int BlogId { get; set; } //ForeignKey，這是 int（不可為 null）
```

由於 `BlogId` 是 `int`（非 nullable），EF Core 會認為這是一個「**必要關聯**」（Required Relationship），也就是說一筆 `Post` 必須要有對應的 `Blog` 才能存在。

因此 EF Core 預設會將刪除行為設定為：

```
DeleteBehavior.Cascade
```

➡️ **當你刪除 Blog 時，所有關聯的 Post 也會一併被刪除。**

---

## 如果改成 `int?` 會怎樣？

```csharp
public int? BlogId { get; set; } // 可為 null
```

這樣關聯就變成「**選擇性關聯**」（Optional），EF Core 的預設行為會變成：

```
DeleteBehavior.ClientSetNull
```

➡️ 刪除 Blog 時，關聯的 Post 不會被刪除，但 `BlogId` 會被設為 `null`（在記憶體/Change Tracker 層級處理）。

---

## 如何手動控制刪除行為？

如果你想要**明確指定**行為（而不是依賴慣例），建議在 `OnModelCreating` 或 Configuration 類別中用 Fluent API 設定：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>()
        .HasOne(p => p.Blog)
        .WithMany(b => b.Posts)
        .HasForeignKey(p => p.BlogId)
        .OnDelete(DeleteBehavior.Cascade); // 明確指定行為
}
```

### 常見的 `DeleteBehavior` 選項：

| 選項 | 說明 |
|---|---|
| `Cascade` | 刪除主表資料時，連帶刪除所有子表資料（預設，適用於必要關聯） |
| `Restrict` | 阻止刪除，若有子表資料存在，刪除主表時會噴錯誤 |
| `SetNull` | 刪除主表資料時，將子表的外鍵設為 `null`（需要外鍵為 nullable） |
| `ClientSetNull` | 類似 SetNull，但邏輯發生在 EF Core 端而非資料庫端（預設，適用於選擇性關聯） |
| `NoAction` | 完全不做任何動作，交給資料庫自行決定（通常會導致外鍵限制錯誤） |

---

## 小提醒 ⚠️

1. **這個 Cascade 行為同時會反映在資料庫層級**：如果你使用 Migrations 產生資料庫，資料庫的外鍵約束（Foreign Key Constraint）也會被設成 `ON DELETE CASCADE`。

2. **如果不想要 Cascade 刪除**，建議明確設定為 `Restrict`，避免不小心刪除主表時，把大量關聯的子表資料也一起刪掉，造成資料誤刪的風險：

```csharp
.OnDelete(DeleteBehavior.Restrict);
```

3. 你也可以用 Data Annotation 加註在 Post 類別上（但通常不建議，因為無法設定 `DeleteBehavior`，只能設定 Required/Optional）：

```csharp
[Required]
public Blog Blog { get; set; }
```

---

## 建議做法

如果這是正式的商業邏輯（例如 Blog 底下的文章很重要，不該被誤刪），建議明確寫出 Fluent API 設定，讓行為清晰、可控，而不是依賴 EF Core 的自動慣例判斷。