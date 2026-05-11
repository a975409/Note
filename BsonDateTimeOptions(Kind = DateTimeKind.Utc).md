## `[BsonDateTimeOptions(Kind = DateTimeKind.Utc)]` 的作用

這個 Attribute 影響的是**讀取（反序列化）**方向，而不是寫入：

```
寫入（序列化）：DateTime → MongoDB
讀取（反序列化）：MongoDB → DateTime
```

| 方向 | 行為 |
|------|------|
| **寫入** | Driver 一律將 DateTime 轉成 UTC 存入，**與此 Attribute 無關** |
| **讀取** | 從 MongoDB 讀出來的時間，**依照 Kind 設定決定回傳的 DateTimeKind** |

---

## 實際差異對比

```csharp
// 情境：MongoDB 存的是 2026-05-07 09:43:00 (UTC)

// ❌ 沒有標記 [BsonDateTimeOptions]
RecordedAt → 2026-05-07 09:43:00，Kind = Local（Driver 預設行為）
// 數值是對的，但 Kind 標記是 Local，語意錯誤

// ✅ 有標記 [BsonDateTimeOptions(Kind = DateTimeKind.Utc)]
RecordedAt → 2026-05-07 09:43:00，Kind = Utc
// 數值正確，Kind 也正確標記為 UTC
```

---

## 為什麼 Kind 很重要

```csharp
var time = recordedAt; // 從 MongoDB 讀出來

// 沒標記：Kind = Local（但實際是 UTC 數值）
time.ToUniversalTime(); // ❌ 會再減 8 小時，變成錯誤時間！

// 有標記：Kind = Utc
time.ToUniversalTime(); // ✅ 不做任何轉換，直接回傳正確時間
```

> 如果沒有標記 `[BsonDateTimeOptions(Kind = DateTimeKind.Utc)]`，讀出來的 `DateTime` 雖然**數值正確**，但 `Kind = Local`，一旦呼叫 `ToUniversalTime()` 或做時區轉換就會**出現 8 小時誤差**。

---

## 你的 Model 設定是正確的 ✅

```csharp
[BsonDateTimeOptions(Kind = DateTimeKind.Utc)]
public DateTime RecordedAt { get; set; } = DateTime.UtcNow;

[BsonDateTimeOptions(Kind = DateTimeKind.Utc)]
[BsonIgnoreIfNull]
public DateTime? ArrivedAt { get; set; } = null;
```

這樣確保：
1. **寫入**：`DateTime.UtcNow` 直接存 UTC，語意明確
2. **讀取**：Driver 讀出來的 `DateTime` 的 `Kind` 會正確標記為 `Utc`，後續做任何時區轉換都不會出錯