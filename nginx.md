
### 如果透過docker啟用`nginx`

1. 查看當前`nginx`的進程，在`powershell`或`docker`容器內的`Terminal`內輸入以下指令：
```
docker top admiring_greider

//結果如下
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                1476                1454                0                   11:06               ?                   00:00:00            nginx: master process nginx -g daemon off;
statd               1519                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1520                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1521                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1522                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1523                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1524                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1525                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1526                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1527                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1528                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1529                1476                0                   11:06               ?                   00:00:00            nginx: worker process
statd               1530                1476                0                   11:06               ?                   00:00:00            nginx: worker process
root                1593                1454                0                   11:15               pts/1               00:00:00            /bin/sh
```

`master process`代表nginx主進程，只會有一個，會管理多個`worker process`，主要讀取或驗證配置文件

`worker process`代表nginx子進程，負責實際的請求