# money-lunch 午饭小助手

同事们一起吃午饭用的小工具：

1. **今天吃什么**：自己填饭店名字，转盘随机抽一家。
2. **饭钱记账**：饭钱统一存在一位同事那里，每天吃了多少就从余额里扣，随时看还剩多少。

金额单位为 **加元（CAD）**。

## 在线使用

| 版本 | 链接 | 数据存在哪 | 适合 |
|---|---|---|---|
| **多人共享版**（推荐） | https://claude.ai/artifact/6weheAjdWcavcL1BmKXZ1e | 云端，所有人看到同一份，实时同步 | 大家一起管饭钱 |
| 单机版 | https://lv5accelerator-xy.github.io/money-lunch/ | 只在自己的浏览器里 | 一个人用，不用登录 |

> 共享版需要用 claude.ai 账号登录，并且要由页面所有者在右上角的 **Share** 菜单里分享给同事：
> 设为 **Contributor（可编辑）** 的人可以加饭店、记账；设为 **Viewer（仅查看）** 的人只能看余额和流水。

## 功能

### 今天吃什么（抽奖转盘）
- 手动输入饭店名字，一次可以加多个（逗号、顿号或换行分隔），重复的会自动去掉。
- 至少 2 家饭店才能抽奖，转盘停在哪家就吃哪家。
- 抽完可以一键跳到记账页，饭店名自动填进备注。
- 共享版会保存「今日已抽中」的结果和抽的人，大家都能看到。

### 饭钱记账
- **存入**：有人把钱交给管钱的同事时记一笔，余额增加。
- **消费**：每天吃饭花了多少记一笔，余额自动扣除。
- 显示当前余额，按日均消费估算还够吃几天；余额为负时提示该补钱。
- 流水按日期分组，显示当天消费合计，以及累计存入、累计消费、本月消费。
- 共享版的每条流水会显示是谁记的。
- 金额按「分」存成整数，不会有小数误差。

### 备份
- 单机版：「导出备份」下载 JSON 文件，「导入备份」恢复（会覆盖当前数据）。
- 共享版：「备份与导入」里可以复制全部数据，也可以导入单机版导出的 JSON（追加，不覆盖）。

## 项目结构

```
.
├── index.html          单机版页面（GitHub Pages 发布的就是它）
├── style.css           单机版样式
├── app.js              单机版逻辑，数据存在 localStorage
├── shared/
│   └── lunch.html      共享版源码，发布在 claude.ai，依赖 claude.ai 的共享数据库
├── .nojekyll           让 GitHub Pages 直接发布静态文件
└── README.md
```

纯 HTML / CSS / JavaScript，没有依赖，不需要构建。

## 本地运行

直接用浏览器打开 `index.html` 即可。或者起一个本地服务器：

```bash
python3 -m http.server 8000
```

然后访问 http://localhost:8000 。

`shared/lunch.html` 只能在 claude.ai 里运行，直接打开会提示「需要在 claude.ai 中打开」。

## 部署

- **单机版**：仓库 Settings → Pages，Source 选 *Deploy from a branch*，分支选 `claude/kind-ride-h1iu4i`（合并后可改为 `main`），目录 `/ (root)`。推送后自动更新。
- **共享版**：修改 `shared/lunch.html` 后，需要在 Claude 里重新发布到同一个 artifact 链接，云端数据不会受影响。

## 数据格式

两个版本的备份 JSON 格式相同：

```json
{
  "restaurants": [{ "name": "兰州拉面" }],
  "transactions": [
    { "type": "deposit", "amount": 50000, "date": "2026-11-17", "note": "小王存入", "created": 1794935802000 },
    { "type": "expense", "amount": 1850,  "date": "2026-11-17", "note": "兰州拉面", "created": 1794935900000 }
  ]
}
```

- `type`：`deposit` 存入，`expense` 消费
- `amount`：金额，单位是**分**（`1850` = $18.50）
- `date`：记账日期 `YYYY-MM-DD`
- `created`：创建时间（毫秒时间戳），用来给同一天的记录排序
