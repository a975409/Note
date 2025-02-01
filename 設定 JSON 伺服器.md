
使用以下指令從 npm 安裝 `json-server`
```
npm install -g json-server
```

在專案的根目錄中，建立一個名為 `db.json` 的檔案
```JSON
{
    "locations": [
        {
            "id": 0,
            "name": "Acme Fresh Start Housing",
            "city": "Chicago",
            "state": "IL",
            "photo": "/assets/bernard-hermant-CLKGGwIBTaY-unsplash.jpg",
            "availableUnits": 4,
            "wifi": true,
            "laundry": true
        },
        {
            "id": 1,
            "name": "A113 Transitional Housing",
            "city": "Santa Monica",
            "state": "CA",
            "photo": "/assets/brandon-griggs-wR11KBaB86U-unsplash.jpg",
            "availableUnits": 0,
            "wifi": false,
            "laundry": true
        },
        {
            "id": 2,
            "name": "Warm Beds Housing Support",
            "city": "Juneau",
            "state": "AK",
            "photo": "/assets/i-do-nothing-but-love-lAyXdl1-Wmc-unsplash.jpg",
            "availableUnits": 1,
            "wifi": false,
            "laundry": false
        },
        {
            "id": 3,
            "name": "Homesteady Housing",
            "city": "Chicago",
            "state": "IL",
            "photo": "/assets/ian-macdonald-W8z6aiwfi1E-unsplash.jpg",
            "availableUnits": 1,
            "wifi": true,
            "laundry": false
        },
        {
            "id": 4,
            "name": "Happy Homes Group",
            "city": "Gary",
            "state": "IN",
            "photo": "/assets/krzysztof-hepner-978RAXoXnH4-unsplash.jpg",
            "availableUnits": 1,
            "wifi": true,
            "laundry": false
        },
        {
            "id": 5,
            "name": "Hopeful Apartment Group",
            "city": "Oakland",
            "state": "CA",
            "photo": "/assets/r-architecture-JvQ0Q5IkeMM-unsplash.jpg",
            "availableUnits": 2,
            "wifi": true,
            "laundry": true
        },
        {
            "id": 6,
            "name": "Seriously Safe Towns",
            "city": "Oakland",
            "state": "CA",
            "photo": "/assets/phil-hearing-IYfp2Ixe9nM-unsplash.jpg",
            "availableUnits": 5,
            "wifi": true,
            "laundry": true
        },
        {
            "id": 7,
            "name": "Hopeful Housing Solutions",
            "city": "Oakland",
            "state": "CA",
            "photo": "/assets/r-architecture-GGupkreKwxA-unsplash.jpg",
            "availableUnits": 2,
            "wifi": true,
            "laundry": true
        },
        {
            "id": 8,
            "name": "Seriously Safe Towns",
            "city": "Oakland",
            "state": "CA",
            "photo": "/assets/saru-robert-9rP3mxf8qWI-unsplash.jpg",
            "availableUnits": 10,
            "wifi": false,
            "laundry": false
        },
        {
            "id": 9,
            "name": "Capital Safe Towns",
            "city": "Portland",
            "state": "OR",
            "photo": "/assets/webaliser-_TPTXZd9mOo-unsplash.jpg",
            "availableUnits": 6,
            "wifi": true,
            "laundry": true
        }
    ]
}
```

在專案的根目錄下執行以下指令
```
json-server --watch db.json
```


建立 `getAllHousingLocations` 函式以呼叫你設定好的 Web 伺服器，取得多筆資料
```typescript
async getAllHousingLocations(): Promise<HousingLocation[]> {
  const data = await fetch(this.url);
  return await data.json() ?? [];
}
```

建立 `getHousingLocationsById` 函式以呼叫你設定好的 Web 伺服器，取得單筆資料
```typescript
async getHousingLocationById(id: number): Promise<HousingLocation | undefined> {
  const data = await fetch(`${this.url}/${id}`);
  return await data.json() ?? {};
}
```

`getAllHousingLocations` 與 `getHousingLocationsById`函式使用非同步程式碼透過 `HTTP` 發出 `get` 請求。

對於此範例，程式碼使用了 `fetch`。對於更高階的用例，請考慮使用 Angular 提供的 [HttpClient](https://angular.tw/api/common/http/HttpClient)
