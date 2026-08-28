# 雨云产品自动续期 (GitHub Actions)

利用 GitHub Actions 定时调用雨云 API，每天自动为指定产品续期（使用积分抵扣）。

- 北京时间**每天 00:00** 自动运行（对应 UTC 16:00）
- 支持随时 **手动触发** 测试
- 敏感信息全部存放在 GitHub Secrets，仓库内无明文

## 一、配置 Secrets

进入仓库 **Settings → Secrets and variables → Actions**，点击 **New repository secret**，添加以下 5 个密钥：

| Secret 名称 | 填写内容 | 说明 |
| :--- | :--- | :--- |
| `RAIN_X_API_KEY` | 你的 API Key | **雨云账户设置 → API密钥** 中获取 |
| `RAIN_DEV_TOKEN` | 你的 Dev Token | **雨云账户设置 → API密钥** 中获取 |
| `PRODUCT_ID` | 产品 ID（数字） | 例如 `73627` |
| `DURATION_DAY` | 续费天数 | 仅可填 `7` 或 `30` |
| `PRODUCT_TYPE` | 产品类别 | 见下方可选值 |

### 产品类别 `PRODUCT_TYPE` 可选值

| 值 | 含义 |
| :--- | :--- |
| `rvh` | 虚拟主机 |
| `rcs` | 云应用 |
| `rgs` | 游戏云 |
| `rbm` | 物理机 |
| `ros` | 对象存储 |
| `rcdn` | 雨云 CDN |

> ⚠️ 以上 3 个变量（`DURATION_DAY`、`PRODUCT_ID`、`PRODUCT_TYPE`）是唯一需要/可以改动的地方，其余保持不变即可。

## 二、文件结构

```
.
├── .github/
│   └── workflows/
│       └── renew.yml    # 工作流配置（每天 00:00 自动续期）
└── README.md
```

## 三、使用方法

### 方式一：自动运行
推送代码后，工作流会按 cron 在北京时间每天 00:00 自动执行。

### 方式二：手动运行（推荐首次先测一次）
1. 进入仓库 **Actions** 选项卡
2. 点击左侧 **雨云产品自动续期**
3. 点击 **Run workflow** → 选择分支 → 再次 **Run workflow**

## 四、修改运行时间

编辑 `.github/workflows/renew.yml` 中的 `cron` 表达式（GitHub 使用 **UTC** 时间）：

```yaml
schedule:
  - cron: '0 16 * * *'   # UTC 16:00 = 北京时间 00:00
```

常见对照（北京时间 → UTC）：

| 北京时间 | UTC Cron |
| :--- | :--- |
| 每天 00:00 | `0 16 * * *` |
| 每天 08:00 | `0 0 * * *` |
| 每天 12:00 | `0 4 * * *` |

## 五、注意事项

1. **仓库类型**：公开仓库可直接使用；私有仓库每月有免费额度，定时任务同样可用。
2. **默认分支**：定时任务仅在默认分支（`main`/`master`）上生效。
3. **天数限制**：`DURATION_DAY` 只能填 `7` 或 `30`。
4. **先手动测一次**：首次使用务必手动运行确认 API 返回成功，再依赖定时任务。

---

Made with ❤️ using GitHub Actions
