# Notion Schema Reference 数据库结构参考

本文件是 Ultra Trader Pro 模板的完整数据库结构参考。录入、复盘、分析模块都依赖此文件。

> **重要**: 以下是默认schema。首次设置时，如果用户的property名称与默认不同，需要建立映射关系并存储到配置中。

---

## 1. 交易记录

**Data Source ID**: `collection://2e4f955a-eb03-8187-8777-000b80e13c4d`

### 需要写入的字段

| Property | Type | 说明 | 必填 |
|----------|------|------|------|
| 股票代码 | title | Ticker，如 AAPL、NVDA | ✅ |
| 方向 | select | `做多` / `做空` | ✅ |
| 入场价 | number (dollar) | 入场价格 | ✅ |
| 出场价 | number (dollar) | 出场价格（持仓中可为空）| ❌ |
| 数量 | number | 交易数量 | ✅ |
| 手续费 | number | Commission | ❌ |
| 状态 | select | `已平仓` / `持仓中` | ✅ |
| 净利润 1 | number | 手动净利润（非formula时填写）| ❌ |

### 日期字段

| Property | 写入格式 |
|----------|---------|
| 开仓日期 | `date:开仓日期:start` = "2026-03-13", `date:开仓日期:is_datetime` = 0 |
| 平仓日期 | `date:平仓日期:start` = "2026-03-13", `date:平仓日期:is_datetime` = 0 |

### Relation 字段（需要先查找关联页面URL）

| Property | 关联数据库 | Data Source ID | Title字段 |
|----------|-----------|---------------|----------|
| 账户 | 主界面 | `collection://2e4f955a-eb03-81d9-9307-000bb19cf948` | 账户 |
| 品种 | 品种分析 | `collection://2e4f955a-eb03-81b3-89a6-000b86d9552b` | 品种 |
| 策略 | 策略分析 | `collection://2e4f955a-eb03-81e3-8f59-000b299c4383` | 策略 |
| 错误类型 | 错误分析 | `collection://2e4f955a-eb03-81a1-a477-000b6ca558fb` | 错误 |
| 交易周期 | 交易周期 | `collection://2e4f955a-eb03-81a2-98bd-000be06e59c1` | (title) |
| 复盘记录 | 复盘日历 | `collection://2e4f955a-eb03-81ea-bbd6-000bb40cc19d` | Name |

### Formula 字段（自动计算，不写入）

净利润、交易结果、提醒、周期标签、复盘(rollup)

---

## 2. 复盘日历

**Data Source ID**: `collection://2e4f955a-eb03-81ea-bbd6-000bb40cc19d`

| Property | Type | 说明 |
|----------|------|------|
| Name | title | 复盘标题，如 "2026-03-13 日复盘" |
| 日期 | date | 复盘日期 |
| 类型 | select | `日复盘` / `周复盘` / `月复盘` / `年复盘` |
| 交易记录 | relation | 关联到交易记录数据库 |
| 违规日志 | relation | 关联到违规日志 |

**Formula字段（自动）**: 胜率, 盈亏比, 交易结果, 净利润, 纪律违规, 违规记录

### 写入复盘示例

```json
{
  "parent": {"data_source_id": "2e4f955a-eb03-81ea-bbd6-000bb40cc19d"},
  "pages": [{
    "properties": {
      "Name": "2026-03-13 日复盘",
      "类型": "日复盘",
      "date:日期:start": "2026-03-13",
      "date:日期:is_datetime": 0,
      "交易记录": "[\"https://notion.so/trade-page-url-1\"]"
    },
    "content": "## 今日复盘\n\n### 执行情况\n[用户回答]\n\n### 情绪状态\n[用户回答]\n\n### 最佳交易\n[用户回答]\n\n### 需要改进\n[用户回答]"
  }]
}
```

---

## 3. 主界面/账户

**Data Source ID**: `collection://2e4f955a-eb03-81d9-9307-000bb19cf948`

- 账户 (title): 如 "主账户"、"模拟账户"、"退休账户"
- 初始资金 (number, dollar)
- 目标 (number, dollar)
- 目标胜率 (number, percent)
- 目标盈亏比 (number)

**Formula字段**: 当前资金, 胜率, 盈亏比, 平均盈利, 平均亏损, 最大回撤, 进度

---

## 4. 策略分析

**Data Source ID**: `collection://2e4f955a-eb03-81e3-8f59-000b299c4383`

- 策略 (title): 如 "基本面分析"、"ABCD"、"期权策略"、"测量"

## 5. 品种分析

**Data Source ID**: `collection://2e4f955a-eb03-81b3-89a6-000b86d9552b`

- 品种 (title): 如 "股票"、"数字货币"

## 6. 错误分析

**Data Source ID**: `collection://2e4f955a-eb03-81a1-a477-000b6ca558fb`

- 错误 (title): 如 "FOMO"、"恐慌卖出"、"分析错误"、"测量"、"恐慌买入"、"回调买入"

---

## Relation 写入规则

写入带有 relation 的交易记录时，必须先获取关联页面的URL：

1. **搜索**: 用 Notion search/fetch 查找目标关联页面
2. **匹配**: 根据 title 匹配用户输入
3. **获取URL**: 取得页面 URL
4. **写入**: 以 JSON array 格式写入 relation 字段

### 完整写入示例

用户输入：NVDA 做多 100股 $175入 $180出 主账户 基本面分析

```
步骤1: 搜索"主账户" → URL: https://notion.so/xxx
步骤2: 搜索"基本面分析" → URL: https://notion.so/yyy
步骤3: 搜索"股票"(品种) → URL: https://notion.so/zzz
步骤4: 创建交易记录
```

```json
{
  "parent": {"data_source_id": "2e4f955a-eb03-8187-8777-000b80e13c4d"},
  "pages": [{
    "properties": {
      "股票代码": "NVDA",
      "方向": "做多",
      "入场价": 175.00,
      "出场价": 180.00,
      "数量": 100,
      "状态": "已平仓",
      "date:开仓日期:start": "2026-03-13",
      "date:开仓日期:is_datetime": 0,
      "date:平仓日期:start": "2026-03-13",
      "date:平仓日期:is_datetime": 0,
      "账户": "[\"https://notion.so/xxx\"]",
      "策略": "[\"https://notion.so/yyy\"]",
      "品种": "[\"https://notion.so/zzz\"]"
    }
  }]
}
```

### 净利润计算（仅当净利润字段为number类型时）

`净利润 = (出场价 - 入场价) × 数量 × 方向系数 - 手续费`
方向系数：做多 = 1，做空 = -1

---

## 用户自定义Schema适配

如果用户的字段名与默认不同，Setup流程会：
1. fetch 用户数据库的实际schema
2. 用户确认字段映射
3. 存储映射到配置
4. 后续操作使用映射后的字段名
