# First-Time Setup Guide 首次配置引导

当用户首次使用此skill或配置信息缺失时，执行以下引导流程。

## 设计原则

- **对话式引导**: 不要一次问完所有问题，分步骤逐个确认
- **智能推断**: 尽量从用户提供的材料中自动推断，减少手动输入
- **容错**: 任何一步都可以跳过，后续补充

---

## Setup Flow 配置流程

### Step 1: 语言版本

**问用户**:
> 你的 Notion 交易模板用的是中文版还是英文版？
> 
> a) 中文版（字段名：方向、入场价、出场价、开仓日期…）
> b) 英文版（字段名：Side、Entry、Exit、Open Date…）

记录 `language: zh / en`。后续所有写入操作只使用对应语言版本的字段名。

### Step 2: 获取数据库信息

**问用户**:
> 请提供你的交易记录数据库的链接（Notion页面URL），我来读取你的数据库结构。
> 
> 或者你也可以发一张数据库的截图给我。

**执行**:
- 如果用户给了 URL → 用 Notion MCP 工具 fetch 获取 schema
- 如果用户给了截图 → 从截图识别字段名
- 记录 `notion_database_url` 和实际 schema

### Step 3: 字段映射

拿到 schema 后，与默认 Ultra Trader Pro schema 对比：

**自动映射规则**（模糊匹配）:
| 可能的用户字段名 | 映射到 |
|---------------|-------|
| ticker/代码/股票/symbol/标的 | 股票代码 (title) |
| 方向/side/direction/buy sell | 方向 |
| 入场/entry/买入价/开仓价 | 入场价 |
| 出场/exit/卖出价/平仓价 | 出场价 |
| 数量/qty/quantity/shares/股数 | 数量 |
| 手续费/fee/commission | 手续费 |
| 账户/account | 账户 |
| 策略/strategy | 策略 |
| 品种/type/instrument/asset | 品种 |
| 日期/date/open date/开仓日期 | 开仓日期 |
| 平仓日期/close date | 平仓日期 |
| 状态/status | 状态 |
| 错误/error/mistake | 错误类型 |

**展示映射结果给用户确认**:
```
我识别到你的数据库有以下字段，映射关系如下：

✅ 股票代码 → ticker（自动匹配）
✅ 方向 → direction（自动匹配）
✅ 入场价 → entry_price（自动匹配）
⚠️ "交易类型" → 不确定，你希望映射到哪个？
   a) 品种（股票/期权/加密货币）
   b) 策略（基本面/技术面）
   c) 跳过

请确认映射是否正确，或告诉我需要调整的地方。
```

### Step 4: 识别 Relation 数据库

对于 relation 类型的字段，需要：

1. 识别关联的数据库及其现有选项
2. 列出所有选项给用户看

**展示**:
```
我检测到以下关联数据库和选项：

📁 账户: 主账户, 模拟账户, 退休账户, 数字货币账户
📁 策略: 基本面分析, ABCD, 期权策略, 测量
📁 品种: 股票, 数字货币, 期权策略
📁 错误类型: FOMO, 恐慌卖出, 分析错误, 测量
📁 交易周期: [列出现有选项]

这些是你目前的选项，录入时我会让你从中选择。
如果需要新增选项，录入时告诉我就行。
```

### Step 5: 券商确认

**问用户**:
> 你主要使用哪个券商？这样我识别截图时会更准确。
> 
> a) Robinhood
> b) IBKR (盈透)
> c) Webull
> d) 其他（请告诉我名字）
> e) 我不用截图录入，跳过

### Step 6: 复盘数据库

**检查**: 是否存在复盘日历数据库

- 如果存在 → 记录 `review_database_url`
- 如果不存在 → 问用户是否要创建一个

### Step 7: 时区确认

**问用户**:
> 你在哪个时区？默认是美东时间 (EST/EDT)。
> 这影响盘前简报和收盘提醒的推送时间。

### Step 8: 保存配置

将所有配置信息写入项目根目录的 `trading-config.md` 文件：

```markdown
## [trading-journal-config]

### Database URLs
- trading_log: [URL]
- review_calendar: [URL]

### Broker
- primary: [Robinhood/IBKR/Webull/其他]

### Language
- template_language: [zh/en]

### Field Mapping
（仅当字段名与默认不同时列出）
- [用户字段名] → [默认字段名]

### Relation Page Mapping
- 账户:
  - 主账户: [page URL]
  - 模拟账户: [page URL]
  - 退休账户: [page URL]
- 策略:
  - 基本面分析: [page URL]
  - ABCD: [page URL]
  - 期权策略: [page URL]
- 品种:
  - 股票: [page URL]
  - 数字货币: [page URL]
- 错误类型:
  - FOMO: [page URL]
  - 恐慌卖出: [page URL]
  - 分析错误: [page URL]

### Timezone
- timezone: America/New_York

### Preferences
- language: zh-CN
- auto_review_reminder: true
- streak_tracking: true
```

### Step 9: 功能验证

配置完成后，建议用户做一次测试：

> 配置完成！让我们测试一下：
> 
> 你可以：
> 1. 运行 `/project:log-trade 做多AAPL 50股 180入` 测试录入
> 2. 运行 `/project:review` 来创建今天的复盘
> 3. 运行 `/project:streak` 查看复盘连续天数

---

## 配置更新

用户可以随时说"更新配置"/"update config" 来修改任何配置项。

常见更新场景：
- 新增策略类型
- 新增账户
- 更换券商
- 修改提醒时间

---

## 错误处理

| 场景 | 处理 |
|------|------|
| 用户给的URL无法访问 | 提示检查Notion集成权限 |
| 数据库没有必要字段 | 列出缺失字段，问是否创建 |
| Relation数据库为空 | 引导用户先添加基本选项 |
| 用户中途放弃setup | 保存已完成的部分，下次继续 |
