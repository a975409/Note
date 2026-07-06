
## 基本操作

| 指令                             | 說明                             |
| ------------------------------ | ------------------------------ |
| `docker compose up -d`         | 啟動所有容器（背景執行）                   |
| `docker compose up --build -d` | 重新 Build 映像後啟動                 |
| `docker compose down`          | 停止並移除容器（**保留** Volume）         |
| `docker compose down -v`       | 停止並移除容器 + **刪除 Volume**（資料會清空） |
| `docker compose ps`            | 查看所有容器狀態                       |

---

## 查看 Log

| 指令 | 說明 |
|------|------|
| `docker logs publicworks-db-1` | 查看 DB 容器 log |
| `docker logs publicworks-api-1` | 查看 API 容器 log |
| `docker logs publicworks-api-1 -f` | 即時追蹤 API log |
| `docker logs publicworks-api-1 --tail 30` | 查看最後 30 行 |
| `docker logs publicworks-api-1 --tail 30 -f` | 查看最後 30 行並即時追蹤 |
| `docker compose logs --tail 20` | 查看所有容器最後 20 行 |

---

## Volume 管理

| 指令 | 說明 |
|------|------|
| `docker volume ls` | 列出所有 Volume |
| `docker volume rm publicworks_sqldata` | 手動刪除指定 Volume |
