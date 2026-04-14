# Trading Journal Sync

**Claude Code Skill** — 让你的 AI 助手成为交易日志管理员

通过截图、Excel 或自然语言，自动将交易记录同步到 Notion 数据库。内置交易分析、复盘提醒、streak 激励和周度策略报告。

专为 **Ultra Trader Pro** Notion 交易模板设计，也支持自定义 Notion 数据库。

---

## 功能一览

### 智能录入
- **截图识别** — 发一张 Robinhood/IBKR/Webull 截图，自动提取交易数据写入 Notion
- **Excel/CSV 导入** — 上传券商导出文件，批量同步到 Notion
- **自然语言** — 直接说 "做多NVDA 100股 175入 180出"，自动录入

### 交易分析
- 按策略/品种/周期/方向的胜率和盈亏统计
- 错误类型频率和成本分析
- 连续盈亏 streak 检测
- Revenge trading 行为模式识别
- R-Multiple 追踪

### 复盘系统
- 结构化复盘引导（执行情况 / 情绪状态 / 最佳最差交易 / 改进点）
- 自动写入 Notion 复盘日历并关联当日交易记录
- 截图归档到交易记录页面

### Streak 激励
- 连续复盘天数追踪（自动跳过周末和休市日）
- 里程碑提醒（7天 / 21天 / 30天 / 100天 / 365天）
- 断链提醒 + 重启鼓励

### Weekly Playbook
- 周度策略报告
- 本周总结 / Best & Worst Trade / 错误统计 / 策略表现
- 下周重大经济数据和财报日历
- 输出到 Notion 复盘日历（类型：周复盘）

---

## 前置要求

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI 或桌面版
- Notion MCP Server 已配置并连接到你的 Notion workspace

### 配置 Notion MCP Server

在你的 Claude Code 设置（`~/.claude/settings.json` 或项目级 `.claude/settings.json`）中添加 Notion MCP Server。具体配置方式取决于你使用的 Notion MCP Server 实现。

---

## 安装

### 方式一：克隆到本地（推荐）

```bash
git clone https://github.com/Rever3l/Finlo-Notion-Trading-Analysis.git
cd Finlo-Notion-Trading-Analysis
claude
```

进入 Claude Code 后，所有命令即可使用。

### 方式二：作为子模块添加到已有项目

```bash
cd your-project
git submodule add https://github.com/Rever3l/Finlo-Notion-Trading-Analysis.git trading-journal
```

然后将 `.claude/commands/` 中的文件复制到你项目的 `.claude/commands/` 目录。

---

## 首次配置

安装后第一次使用时，运行：

```
/project:setup
```

进入**对话式配置流程**，全程不需要写代码：

1. **提供 Notion 数据库链接** — 发送你的交易记录数据库 URL
2. **确认字段映射** — 自动读取你的 schema 并尝试匹配
3. **选择券商** — 提高截图识别准确率
4. **确认关联选项** — 账户、策略、品种等已有选项
5. **测试录入** — 描述一笔交易验证

配置完成后信息存储在 `trading-config.md` 中，后续使用无需重复配置。

> **Ultra Trader Pro 用户**：你的数据库已经完美适配默认 schema，配置步骤会更快。

---

## 使用方式

### 斜杠命令

| 命令 | 说明 |
|------|------|
| `/project:setup` | 首次配置 / 重新配置 |
| `/project:log-trade` | 录入交易（支持自然语言描述） |
| `/project:review` | 开始今日复盘 |
| `/project:streak` | 查看复盘连续天数 |
| `/project:analyze` | 交易模式分析报告 |
| `/project:weekly-playbook` | 周度策略报告 |
| `/project:pre-market` | 盘前简报 |
| `/project:post-market` | 收盘汇总 + 复盘提醒 |

### 自然语言（自动识别）

你也可以直接用自然语言，Claude Code 会根据 `CLAUDE.md` 中的路由规则自动识别意图：

```
# 录入交易
做多AAPL 50股 228入 235出 主账户 基本面分析

# 复盘
复盘

# 分析
分析我的交易

# 周报
周报
```

### 带参数的命令

```
/project:log-trade 做多NVDA 100股 175入 180出
/project:analyze 最近20笔
```

---

## 文件结构

```
Finlo-Notion-Trading-Analysis/
├── CLAUDE.md                        # 主指令文件（自动检测 + 模式路由）
├── .claude/
│   └── commands/
│       ├── setup.md                 # /project:setup
│       ├── log-trade.md             # /project:log-trade
│       ├── review.md                # /project:review
│       ├── streak.md                # /project:streak
│       ├── analyze.md               # /project:analyze
│       ├── weekly-playbook.md       # /project:weekly-playbook
│       ├── pre-market.md            # /project:pre-market
│       └── post-market.md           # /project:post-market
├── references/
│   ├── notion-schema.md             # Notion 数据库 schema + 写入规则
│   ├── setup-guide.md               # 首次配置对话式引导
│   └── analysis-rules.md            # 分析规则 + Streak + Weekly Playbook
├── README.md
└── LICENSE
```

---

## 适配的 Notion 模板

本工具默认适配 **Ultra Trader Pro** 交易模板的数据库结构，包括：

- 交易记录（支持 relation 关联：账户、策略、品种、错误类型、交易周期）
- 复盘日历（日/周/月/年复盘）
- 策略分析
- 品种分析
- 错误分析
- 账户仪表盘

**也支持自定义 Notion 数据库** — 首次配置时会自动读取你的 schema 并建立字段映射。

---

## 隐私与安全

- 所有数据仅在 Claude Code 和 Notion 之间传输
- 不收集、不存储、不上传任何交易数据到第三方
- 截图仅用于识别，不外传
- 配置信息存储在本地 `trading-config.md` 中

---

## 反馈与贡献

- **Bug / 功能建议**: [GitHub Issues](https://github.com/Rever3l/Finlo-Notion-Trading-Analysis/issues)

---

## License

MIT

---

*Built by [Finlo](https://github.com/Rever3l) — Notion 交易系统模板*
