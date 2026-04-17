# BTC Cycle Bottom Signal Tracker

一页仪表盘,每日追踪 5 个底部反转信号 + DCA 进度 + 杠杆就绪度。

## 文件结构

- `dashboard.html` — 仪表盘主页(打开即可)
- `data.json` — 每日更新的数据(只改这个,页面自动刷新)
- `CLAUDE.md` — 框架说明(5 个信号定义、DCA 规则、杠杆条件)

## 如何打开

由于浏览器限制 `file://` 下的 `fetch`,需用本地 server:

```bash
cd ~/btc-prediction
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000/dashboard.html
```

或者直接让 Claude 帮你跑 server。

## 每日更新流程

每天只改 `data.json` —— 关键字段:

### 必改字段
```json
"updated": "2026-04-17",            // 今天日期
"btc": {
  "price": 75428,                   // 当前 BTC 价格
  "recent_pullback_low": 66000      // 最近一次回踩低点
},
"cycle": {
  "days_from_top": 193              // 距 2025-10-06 顶部天数
}
```

### 信号状态(`signals[*].status`)
每个信号三选一:
- `"triggered"` — 完全触发(绿色)
- `"partial"` — 部分触发 / 正在构筑(黄色)
- `"pending"` — 未触发(灰色)

同时更新对应的 `current`(当前情况)和 `gap`(距离触发还差什么)。

### DCA 字段(每周更新)
```json
"dca": {
  "week": 1,                        // 当前第几周
  "deployed_usd": 0,                // 累计已买入金额
  "deployed_pct": 0,                // 占剩余 30% 的百分比
  "this_week_buy_usd": 75000,       // 本周买入金额(买完后填)
  "this_week_buy_price": 75428      // 本周买入均价
}
```

### 事件列表(`events_next_week`)
把本周 / 下周的重要事件填进去(FOMC、CPI、地缘政治等)。

## 状态颜色速查

| 颜色 | 含义 | 杠杆建议 |
|------|------|---------|
| 🟢 绿 | 4/5 triggered | **EXECUTE LEVERAGE** |
| 🟠 橙 | 2+ triggered 或 4+ triggered/partial | **PREPARE LEVERAGE** |
| 🟡 黄 | 其他 | **CONTINUE DCA** |

## 信号卡片读法

每个信号卡显示 4 块:
1. **当前状态 (Current)** — 最新数据
2. **触发条件 (Trigger)** — 需要满足什么
3. **距离触发还差什么 (Gap)** — 最关键,告诉你还需要发生什么
4. **关键位 (Key Level)** — 具体价格/数值阈值

底部有历史参考(2019/2022 年的类似信号)。

## 让 Claude 帮你更新

每次新会话跟 Claude 说:
> 更新仪表盘数据

Claude 会搜网最新数据、算回撤/周期进度、重写 `data.json`,然后你刷新浏览器即可。
