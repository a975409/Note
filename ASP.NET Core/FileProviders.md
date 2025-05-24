安裝套件： Microsoft.Extensions.FileProviders.Physical

### 原始碼說明

1. IChangeToken：監控資料變化的"權杖"，原始碼請參考：[IChangeToken 介面 (Microsoft.Extensions.Primitives) | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.extensions.primitives.ichangetoken?view=dotnet-plat-ext-7.0)
    
2. CancellationChangeToken：[CancellationChangeToken 類別 (Microsoft.Extensions.Primitives) | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.extensions.primitives.cancellationchangetoken?view=dotnet-plat-ext-7.0)
    
3. CompositeChangeToken：[CompositeChangeToken 類別 (Microsoft.Extensions.Primitives) | Microsoft Learn](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.extensions.primitives.compositechangetoken?view=dotnet-plat-ext-7.0)
    
    代表一或多個 [IChangeToken](https://learn.microsoft.com/zh-tw/dotnet/api/microsoft.extensions.primitives.ichangetoken?view=dotnet-plat-ext-7.0) 執行個體。 HasChanged 屬性：當任一 IChangeToken 發生改變，該值就會為 true ActiveChangeCallbacks 屬性：任一 IChangeToken 同名屬性發生改變，該值就會為 true
    
4. 以下為繼承IFileProvider介面的類別：
    
    - PhysicalFileProvider：實體檔的檔案系統，使用PhysicalFileInfo描述檔案、PhysicalDirectoryInfo描述目錄
    - EmbeddedFileProvider：程式集內嵌檔的檔案系統，所有資源檔都歸在程式集的根目錄下，並沒有目錄層級的概念
    - NullFileProvider：不包含任何目錄、檔案的空檔案系統
    - CompositeFileProvider：多個IFileProvider介面的複合式檔案系統

以下為實際案例：
[[樹狀層級結構]]
[[讀取檔案內容]]
[[監控檔案的變化]]