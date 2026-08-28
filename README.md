# 雨云产品自动续期 (GitHub Actions)

每天北京时间 **00:00** 自动调用雨云 API 续期产品（积分抵扣）。

严格基于官方 curl 模板：**只把 `x-api-key`、`duration_day`、`product_id`、`product_type` 这 4 处替换为 GitHub 机密，其余部分（`--location --request POST`、请求 URL、`rain-dev-token` 请求头、`Content-Type` 请求头、`--data-raw` 的 JSON 结构及引号格式）全部保持模板原样未改动。**

## 一、需要配置的机密（Secrets）

进入仓库 **Settings → Secrets and variables → Actions → New repository secret**，添加以下 **4 个**：

| Secret 名称 | 对应模板位置 | 填写内容 |
| :--- | :--- | :--- |
| `RAIN_API` | `x-api-key:` | **雨云API**：到「账户设置 → API密钥」中获取 |
| `DURATION_DAY` | `"duration_day":` | **续费天数**：仅可填 `7` 或 `30` |
| `PRODUCT_ID` | `"product_id":` | **产品ID**：数字（如 `73627`） |
| `PRODUCT_TYPE` | `"product_type":` | **产品类别**：见下表 |

### 产品类别 `PRODUCT_TYPE` 可选值

| 值 | 含义 |
| :--- | :--- |
| `rvh` | 虚拟主机 |
| `rcs` | 云应用 |
| `rgs` | 游戏云 |
| `rbm` | 物理机 |
| `ros` | 对象存储 |
| `rcdn` | 雨云 CDN |

## 二、保持原样未动的部分

模板中除上述 4 处外的所有内容均未改动，包括：

- `--location --request POST` 参数
- 请求 URL：`https://api.v2.rainyun.com/product/point_renew`
- `rain-dev-token: {{rain-dev-token}}` 请求头（按你的要求保持模板原样）
- `Content-Type: application/json` 请求头
- `--data-raw` 的 JSON 结构，以及 `duration_day`/`product_id` 不带引号、`product_type` 带引号的格式

> **注意**：`rain-dev-token` 按你的要求保持了模板原字面量 `{{rain-dev-token}}`。它不含 `$` 前缀，在 GitHub Actions 中不会被当作表达式解析，会原样发送给服务器。**若雨云要求这里也填真实 token、需要改成第 5 个机密，告诉我一声即可。**

## 三、文件结构

```
.
├── .github/
│   └── workflows/
│       └── renew.yml    # 工作流（每天 00:00 自动运行）
└── README.md
```

## 四、使用方法

**自动运行**：推送到默认分支后，每天北京时间 00:00 自动执行。

**手动测试**（推荐首次先跑一次）：
1. 仓库 **Actions** 选项卡
2. 点击左侧 **雨云产品自动续期**
3. **Run workflow** → 选分支 → 再次 **Run workflow**

## 五、修改运行时间

编辑 `.github/workflows/renew.yml` 的 `cron`（GitHub 使用 **UTC** 时间）：

```yaml
schedule:
  - cron: '0 16 * * *'   # UTC 16:00 = 北京时间 00:00
```

| 北京时间 | UTC Cron |
| :--- | :--- |
| 每天 00:00 | `0 16 * * *` |
| 每天 08:00 | `0 0 * * *` |
| 每天 12:00 | `0 4 * * *` |

## 六、注意事项

1. **仓库类型**：公开仓库直接用；私有仓库每月有免费额度，定时任务同样可用。
2. **默认分支**：定时任务仅在默认分支（`main`/`master`）生效。
3. **先手动测**：首次务必手动运行一次确认 API 返回成功，再依赖定时任务。
