# 收盘提醒 Post-Market Nudge

生成收盘后的交易汇总并提醒复盘。建议在每个交易日收盘后运行。

## 前置检查

1. 读取 `trading-config.md` 确认已完成配置
2. 读取 `references/notion-schema.md` 获取数据库信息

## 执行步骤

1. 使用 Notion MCP 工具读取当日交易记录
2. 生成当日交易摘要：
   - 交易笔数
   - 总 P&L
   - 最佳 / 最差交易
   - 错误类型统计（如有）
3. 提醒用户进行复盘
4. 如果用户同意，自动切换到复盘流程（参考 `/project:review`）

## 输出格式

```
收盘汇总 | [日期]

今日交易: {n} 笔
总 P&L: ${amount}
最佳: {ticker} +${amount}
最差: {ticker} -${amount}

该复盘了！输入 /project:review 开始今日复盘。
```

## 用户输入

$ARGUMENTS
