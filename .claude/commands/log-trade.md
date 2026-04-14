# 录入交易记录

支持三种录入方式：截图识别、文件导入、自然语言。

## 前置检查

1. 读取 `trading-config.md` 确认已完成配置。如未配置，提示用户先运行 `/project:setup`
2. 读取 `references/notion-schema.md` 获取字段映射和写入规则

## 截图识别 (Screenshot Import)

1. 根据 UI 特征判断券商：Robinhood / IBKR / Webull / 其他
2. 提取数据：Ticker, Side, Entry/Exit, Quantity, Date, Commission
3. 结构化展示识别结果，让用户确认或修正
4. 询问截图中无法获取的字段（策略、账户等），展示已有选项供选择
5. 使用 Notion MCP 工具查找 relation 页面并创建交易记录
6. 截图嵌入页面正文作为归档
7. 返回写入成功的 Notion 链接

## 文件导入 (File Import)

1. 解析 CSV/Excel 文件表头
2. 自动映射字段 + 用户确认
3. 预览前 5 条记录
4. 逐条写入 Notion，处理 relation
5. 汇总成功/失败统计

## 自然语言录入 (NL Import)

解析如 "今天做多NVDA 112股 175入 180出" 的自然语言，同截图流程。

## Relation 写入规则

写入带 relation 的记录时，必须先通过 Notion MCP 工具搜索/获取关联页面 URL，再以 JSON array 格式写入。

## 净利润计算

`净利润 = (出场价 - 入场价) x 数量 x 方向系数 - 手续费`
方向系数：做多 = 1，做空 = -1

## 用户输入

$ARGUMENTS
