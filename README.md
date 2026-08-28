fork本仓库
# Rainyun 自动续期

每天北京时间 00:00 自动调用雨云 API 续期产品。

## 配置 Secrets

进入仓库 Settings → Secrets → Actions，添加：

| Secret 名称 | 说明 |
|---|---|
| `RAIN_DEV_TOKEN` | 雨云 Dev Token |
| `RAIN_X_API_KEY` | API Key |
| `PRODUCT_ID` | 产品 ID（数字） |
| `DURATION_DAY` | 续期天数（数字） |

## 运行

- **自动**：每天北京时间 00:00 自动执行
- **手动**：Actions 页面 → 选择工作流 → Run workflow

## 修改时间

编辑 `.github/workflows/renew.yml` 中的 cron 表达式：
- UTC 16:00 = 北京时间 00:00（`0 16 * * *`）
- 改其他时间请自行换算时区
