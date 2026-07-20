1. 用命令列參數執行：
```shell
.\daliAPI.exe --environment Staging
```

2. 用命令列參數執行，並指定appsettings.json設定檔
```shell
.\daliAPI.exe --environment Development
```

3. 用命令列參數執行，並指定監聽的Url
```shell
.\daliAPI.exe --urls "http://0.0.0.0:8080"
```

4. 同時指定環境 + Port（組合使用）
```shell
.\daliAPI.exe --environment Production --urls "http://0.0.0.0:8080"
```