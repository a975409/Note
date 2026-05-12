
```
DateTime.Now  →  本地時間 (UTC+8)  →  MongoDB Driver 自動轉換  →  儲存為 UTC 時間
例：2026-05-07 17:27:00 (台灣)    →   2026-05-07 09:27:00 (UTC)
```

MongoDB 內部**統一用 UTC 儲存所有時間**，C# Driver 在序列化時會自動把 `DateTime.Now`（Local Kind）轉成 UTC。

## 解法：統一使用 `DateTime.UtcNow`

這是**最推薦的做法**，不要用 `DateTime.Now`，改成 `DateTime.UtcNow`，讓儲存與顯示的語意一致：

### `AmbulanceHub.cs` 修正

```csharp
// ❌ 修正前
RecordedAt = DateTime.Now,

// ✅ 修正後
RecordedAt = DateTime.UtcNow,
```

## 前端顯示時再轉換本地時間

資料庫統一存 UTC，**顯示時才轉換**，這是業界標準做法：

```
MongoDB 儲存          後端回傳 API          前端顯示
UTC 09:27:00    →    UTC 09:27:00    →    轉換成 UTC+8 → 17:27:00
```

### 前端 JavaScript 轉換範例
```javascript
// UTC 時間字串 → 本地時間顯示
const utcTime = "2026-05-07T09:27:00Z";
const localTime = new Date(utcTime).toLocaleString('zh-TW');
// 輸出：2026/5/7 下午5:27:00
```

### 後端 C# 如果需要轉換顯示
```csharp
// UTC → 台灣時間
var taiwanZone = TimeZoneInfo.FindSystemTimeZoneById("Taipei Standard Time");
var localTime = TimeZoneInfo.ConvertTimeFromUtc(utcTime, taiwanZone);
```

## 總結

| 方式                     | 建議                          |
| ---------------------- | --------------------------- |
| 儲存時用 `DateTime.Now`    | ❌ 語意不清，Driver 會自動轉 UTC，容易混淆 |
| 儲存時用 `DateTime.UtcNow` | ✅ 明確表示 UTC，與 MongoDB 儲存行為一致 |
| 顯示時在前端/後端轉換時區          | ✅ 統一在最後一層處理時區顯示             |