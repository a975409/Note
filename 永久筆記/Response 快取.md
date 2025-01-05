[[Response 設定Middleware快取]]
[[HTTP 型的Response快取]]
[[禁用指定的Action的Response 快取]]

回應快取選項：

| 選項                                                                                                                                                                                                                                        | 描述                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| [MaximumBodySize](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.responsecaching.responsecachingoptions.maximumbodysize#microsoft-aspnetcore-responsecaching-responsecachingoptions-maximumbodysize)                   | 回應主體的最大可快取大小，以位元組為單位。 預設值為 `64 * 1024 * 1024` (64 MB)。    |
| [SizeLimit](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.responsecaching.responsecachingoptions.sizelimit#microsoft-aspnetcore-responsecaching-responsecachingoptions-sizelimit)                                     | 回應快取中介軟體的大小限制，以位元組為單位。 預設值為 `100 * 1024 * 1024` (100 MB)。 |
| [UseCaseSensitivePaths](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.aspnetcore.responsecaching.responsecachingoptions.usecasesensitivepaths#microsoft-aspnetcore-responsecaching-responsecachingoptions-usecasesensitivepaths) | 判斷回應是否在區分大小寫的路徑上快取。 預設值是 `false`。                         |

