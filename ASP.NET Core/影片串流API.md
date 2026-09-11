```C#
[Route("api/[controller]")]
[ApiController]
public class t_OnSiteVideoApiController : ControllerBase

[HttpGet("stream/{id:int}")]
public async Task<IActionResult> StreamVideo(int id)
{
    var video = await _t_OnSiteVideoService.GetVideoByIdAsync(id);

    if (video?.Video == null)
        return NotFound();

    var videoBytes = video.Video;
    var totalLength = videoBytes.Length;
    const string contentType = "video/mp4"; // 依實際格式調整

    // 解析 Range Header
    var rangeHeader = Request.Headers["Range"].ToString();

    if (string.IsNullOrEmpty(rangeHeader))
    {
        // 無 Range：直接回傳全部（小影片或不支援 seek 的情境）
        return File(videoBytes, contentType, enableRangeProcessing: true);
    }

    // 解析 Range: bytes=start-end
    var range = HttpHeaderSetting.ParseRangeHeader(rangeHeader, totalLength);
    if (range == null)
        return StatusCode(416); // Range Not Satisfiable

    var (start, end) = range.Value;
    var chunkLength = end - start + 1;

    Response.StatusCode = 206; // Partial Content
    Response.Headers["Content-Range"] = $"bytes {start}-{end}/{totalLength}";
    Response.Headers["Accept-Ranges"] = "bytes";
    Response.Headers["Content-Length"] = chunkLength.ToString();
    Response.ContentType = contentType;

    await Response.Body.WriteAsync(videoBytes, start, chunkLength);
    return new EmptyResult();
}
```

ParseRangeHeader：
```C#
// ─────────────────────────────────────────────
// Range Header 解析工具
// ─────────────────────────────────────────────
public static (int start, int end)? ParseRangeHeader(string rangeHeader, int totalLength)
{
    // 格式：bytes=0-1023
    if (!rangeHeader.StartsWith("bytes=")) return null;

    var range = rangeHeader["bytes=".Length..].Split('-');
    if (range.Length != 2) return null;

    int start = string.IsNullOrEmpty(range[0]) ? 0 : int.Parse(range[0]);
    int end = string.IsNullOrEmpty(range[1]) ? totalLength - 1 : int.Parse(range[1]);

    end = Math.Min(end, totalLength - 1);

    if (start > end) return null;

    return (start, end);
}
```

前端：
```html
<video controls preload="metadata">
  <source src="https://yourserver.com/api/t_OnSiteVideoApi/stream/123" type="video/mp4">
</video>
```

## 整體流程圖

```ini
前端                          後端
 │                              │
 │  GET /not-binding            │
 ├─────────────────────────────>│  查詢清單（不含 byte[]）
 │<─────────────────────────────┤  回傳 id + streamUrl 清單
 │                              │
 │  渲染 <video> 標籤           │
 │  (preload="metadata")        │
 │                              │
 │  GET /stream/123             │
 │  Range: bytes=0-             │
 ├─────────────────────────────>│  依 Range 回傳片段
 │<─────────────────────────────┤  206 Partial Content
 │                              │
 │  使用者拖曳進度條             │
 │  GET /stream/123             │
 │  Range: bytes=500000-        │
 ├─────────────────────────────>│  只回傳該片段
 │<─────────────────────────────┤  206 Partial Content
```

## 重點整理
# 影片串流 API 開發方案

## 核心問題分析

直接回傳 `byte[]` 有以下問題：
- ❌ 需等待所有影片載入完畢才回傳 → 等待時間長
- ❌ 前端無法 seek（拖曳進度條）
- ❌ 記憶體佔用大

## 解決方案：分離「清單查詢」與「影片串流」

### 架構設計

```
GET /api/video/not-binding          → 回傳影片清單（不含 byte[]）
GET /api/video/stream/{id}          → 單支影片串流（支援 Range Request）
```

---

## 實作

### Step 1：建立 DTO（清單不含影片本體）

```csharp
public class OnSiteVideoListItemDto
{
    public int Id { get; set; }
    public string? Uploader { get; set; }
    public string? LineGroupId { get; set; }
    public DateTime? CreateAt { get; set; }

    /// <summary>
    /// 前端用來播放的串流網址
    /// </summary>
    public string StreamUrl { get; set; } = string.Empty;
}
```

---

### Step 2：修改 Repository，新增單筆影片查詢

```csharp
/// <summary>
/// 依 Id 取得單筆影片 byte[]（僅供串流使用）
/// </summary>
public async Task<t_OnSiteVideo?> GetVideoByIdAsync(int id)
{
    return await _context.t_OnSiteVideo
        .AsNoTracking()
        .Where(x => x.Id == id)
        .Select(x => new t_OnSiteVideo
        {
            Id = x.Id,
            Video = x.Video,       // 只在這裡才撈 byte[]
            LineGroupId = x.LineGroupId
        })
        .FirstOrDefaultAsync();
}
```

---

### Step 3：Controller 實作

```csharp
[ApiController]
[Route("api/[controller]")]
public class OnSiteVideoController : ControllerBase
{
    private readonly t_OnSiteVideoRepository _videoRepository;
    private readonly IHttpContextAccessor _httpContextAccessor;
    private const int Limit = 20;

    public OnSiteVideoController(
        t_OnSiteVideoRepository videoRepository,
        IHttpContextAccessor httpContextAccessor)
    {
        _videoRepository = videoRepository;
        _httpContextAccessor = httpContextAccessor;
    }

    // ─────────────────────────────────────────────
    // 1. 清單 API：快速回傳，不含影片 byte[]
    // ─────────────────────────────────────────────
    [HttpGet("not-binding")]
    public async Task<IActionResult> GetNotBindingList([FromQuery] Search_NotBinding_OnSiteImageDto dto)
    {
        var result = await _videoRepository.GetNotBindingOnSiteImagesAsync(dto, Limit);

        // 將清單轉成 DTO，附上串流網址，不回傳 Video byte[]
        var baseUrl = $"{Request.Scheme}://{Request.Host}";

        var listDto = new
        {
            result.TotalCount,
            result.TotalPages,
            result.CurrentPage,
            Items = result.Items.Select(x => new OnSiteVideoListItemDto
            {
                Id        = x.Id,
                Uploader  = x.Uploader,
                LineGroupId = x.LineGroupId,
                CreateAt  = x.CreateAt,
                StreamUrl = $"{baseUrl}/api/onsitevideo/stream/{x.Id}"
            })
        };

        return Ok(listDto);
    }

    // ─────────────────────────────────────────────
    // 2. 串流 API：支援 Range Request（可拖曳進度條）
    // ─────────────────────────────────────────────
    [HttpGet("stream/{id:int}")]
    public async Task<IActionResult> StreamVideo(int id)
    {
        var video = await _videoRepository.GetVideoByIdAsync(id);

        if (video?.Video == null)
            return NotFound();

        var videoBytes = video.Video;
        var totalLength = videoBytes.Length;
        const string contentType = "video/mp4"; // 依實際格式調整

        // 解析 Range Header
        var rangeHeader = Request.Headers["Range"].ToString();

        if (string.IsNullOrEmpty(rangeHeader))
        {
            // 無 Range：直接回傳全部（小影片或不支援 seek 的情境）
            return File(videoBytes, contentType, enableRangeProcessing: true);
        }

        // 解析 Range: bytes=start-end
        var range = ParseRangeHeader(rangeHeader, totalLength);
        if (range == null)
            return StatusCode(416); // Range Not Satisfiable

        var (start, end) = range.Value;
        var chunkLength = end - start + 1;

        Response.StatusCode = 206; // Partial Content
        Response.Headers["Content-Range"]  = $"bytes {start}-{end}/{totalLength}";
        Response.Headers["Accept-Ranges"]  = "bytes";
        Response.Headers["Content-Length"] = chunkLength.ToString();
        Response.ContentType = contentType;

        await Response.Body.WriteAsync(videoBytes, start, chunkLength);
        return new EmptyResult();
    }

    // ─────────────────────────────────────────────
    // Range Header 解析工具
    // ─────────────────────────────────────────────
    private static (int start, int end)? ParseRangeHeader(string rangeHeader, int totalLength)
    {
        // 格式：bytes=0-1023
        if (!rangeHeader.StartsWith("bytes=")) return null;

        var range = rangeHeader["bytes=".Length..].Split('-');
        if (range.Length != 2) return null;

        int start = string.IsNullOrEmpty(range[0]) ? 0 : int.Parse(range[0]);
        int end   = string.IsNullOrEmpty(range[1]) ? totalLength - 1 : int.Parse(range[1]);

        end = Math.Min(end, totalLength - 1);

        if (start > end) return null;

        return (start, end);
    }
}
```

---

## 前端播放（HTML 範例）

```html
<!-- 取得清單後，動態產生 video 標籤 -->
<video controls preload="metadata">
  <source src="/api/onsitevideo/stream/123" type="video/mp4" />
</video>
```

```javascript
// 取得清單
const res = await fetch('/api/onsitevideo/not-binding?page=1&startTime=...&endTime=...');
const data = await res.json();

data.items.forEach(item => {
  const video = document.createElement('video');
  video.controls = true;
  video.preload = 'metadata'; // 只預載 metadata，不預載影片本體
  
  const source = document.createElement('source');
  source.src = item.streamUrl;  // 直接使用回傳的串流網址
  source.type = 'video/mp4';
  
  video.appendChild(source);
  document.body.appendChild(video);
});
```

---

## 整體流程圖

```
前端                          後端
 │                              │
 │  GET /not-binding            │
 ├─────────────────────────────>│  查詢清單（不含 byte[]）
 │<─────────────────────────────┤  回傳 id + streamUrl 清單
 │                              │
 │  渲染 <video> 標籤           │
 │  (preload="metadata")        │
 │                              │
 │  GET /stream/123             │
 │  Range: bytes=0-             │
 ├─────────────────────────────>│  依 Range 回傳片段
 │<─────────────────────────────┤  206 Partial Content
 │                              │
 │  使用者拖曳進度條             │
 │  GET /stream/123             │
 │  Range: bytes=500000-        │
 ├─────────────────────────────>│  只回傳該片段
 │<─────────────────────────────┤  206 Partial Content
```

## 重點整理

| 問題        | 解法                                                 |
| --------- | -------------------------------------------------- |
| 清單回傳慢     | 查詢時 **不 SELECT Video 欄位**，只回傳 metadata + streamUrl |
| 影片無法 seek | 實作 **HTTP Range Request**，回傳 `206 Partial Content` |
| 多支影片同時載入  | 前端使用 `preload="metadata"`，只在播放時才請求影片資料             |
| 記憶體壓力     | 可進一步改為 `Stream` 方式讀取，避免整個 `byte[]` 載入記憶體           |

> ⚠️ **補充建議**：若影片檔案很大，建議考慮將影片從資料庫移至 **Azure Blob Storage / MinIO** 等物件儲存，直接提供串流 URL，效能會更好。