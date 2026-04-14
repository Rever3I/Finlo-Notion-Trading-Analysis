# 盘前简报 Pre-Market Brief

生成每日盘前简报。建议在每个交易日开盘前运行。

## 前置检查

1. 读取 `trading-config.md` 确认已完成配置
2. 读取 `references/analysis-rules.md` 的 Pre-Market Brief 部分

## 内容生成

### 1. 持仓状态

使用 Notion MCP 工具查询状态为 "持仓中" 的交易记录，展示：
- Ticker / 方向 / 数量 / 入场价
- 通过 web search 获取当前价格，计算浮盈

### 2. 今日到期

检查是否有到期的期权或计划平仓的交易。

### 3. 注意事项

基于历史交易 pattern 生成个性化提醒：
- 如果当前处于连续盈利 streak → 提醒谨慎
- 如果昨天有亏损 → 提醒避免 revenge trading
- 如果本周交易频率异常高 → 提醒控制节奏
- 如果最近某个错误类型频繁出现 → 专门提醒

### 4. Pre-Trade Checklist

- [ ] 市场整体方向判断？
- [ ] 今天有没有重大数据/事件？
- [ ] 止损位确定了吗？
- [ ] 仓位大小合理吗？
- [ ] 这笔交易符合你的策略框架吗？

## 用户输入

$ARGUMENTS
