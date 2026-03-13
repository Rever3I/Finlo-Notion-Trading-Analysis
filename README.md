# 🦞 Trading Journal Sync

**OpenClaw Skill** — 让你的 AI 助手成为交易日志管理员

通过截图、Excel 或自然语言，自动将交易记录同步到 Notion 数据库。内置交易分析、复盘提醒、streak 激励和周度策略报告。

专为 **Ultra Trader Pro** Notion 交易模板设计，也支持自定义 Notion 数据库。

---

## ✨ 功能一览

### 📸 智能录入
- **截图识别** — 发一张 Robinhood/IBKR/Webull 截图，自动提取交易数据写入 Notion
- **Excel/CSV 导入** — 上传券商导出文件，批量同步到 Notion
- **自然语言** — 直接说 "做多NVDA 100股 175入 180出"，自动录入

### 📊 交易分析
- 按策略/品种/周期/方向的胜率和盈亏统计
- 错误类型频率和成本分析
- 连续盈亏 streak 检测
- Revenge trading 行为模式识别
- R-Multiple 追踪

### 📝 复盘系统
- 收盘后自动推送复盘提醒（4:15 PM EST）
- 结构化复盘引导（执行情况 / 情绪状态 / 最佳最差交易 / 改进点）
- 自动写入 Notion 复盘日历并关联当日交易记录
- 截图归档到交易记录页面

### 🔥 Streak 激励
- 连续复盘天数追踪（自动跳过周末和休市日）
- 里程碑提醒（7天 / 21天 / 30天 / 100天 / 365天）
- 断链提醒 + 重启鼓励

### 📋 Weekly Playbook
- 每周日自动生成周度策略报告
- 本周总结 / Best & Worst Trade / 错误统计 / 策略表现
- 下周重大经济数据和财报日历
- 输出到 Notion 复盘日历（类型：周复盘）

---

## 🚀 安装

### 方式一：ClawHub（推荐）

```bash
clawhub install finlo-notion-trading-analysis
```

### 方式二：GitHub 链接

直接在 OpenClaw 对话中发送：

```
Install this skill: https://github.com/Rever3l/Finlo-Notion-Trading-Analysis
```

### 方式三：手动安装

```bash
cd ~/.openclaw/skills
git clone https://github.com/Rever3l/Finlo-Notion-Trading-Analysis.git
```

重启 OpenClaw 会话使 skill 生效。

---

## ⚙️ 首次配置

安装后第一次使用时，skill 会自动进入**对话式配置流程**，全程不需要写代码：

1. **提供 Notion 数据库链接** — 发送你的交易记录数据库 URL
2. **确认字段映射** — skill 自动读取你的 schema 并尝试匹配
3. **选择券商** — 提高截图识别准确率
4. **确认关联选项** — 账户、策略、品种等已有选项
5. **测试录入** — 发一张截图或描述一笔交易验证

配置完成后信息存储在你的 `USER.md` 中，后续使用无需重复配置。

> 💡 **Ultra Trader Pro 用户**：你的数据库已经完美适配默认 schema，配置步骤会更快。

---

## 📖 使用方式

### 录入交易

```
# 发截图
[直接发送一张券商交易截图]

# 自然语言
做多AAPL 50股 228入 235出 主账户 基本面分析

# Excel导入
[发送CSV/Excel文件]
```

### 复盘

```
复盘          # 开始今日复盘
streak        # 查看复盘连续天数
```

### 分析

```
分析我的交易   # Pattern Recognition 报告
周报          # Weekly Playbook
```

### 管理

```
设置交易日志   # 重新运行配置
更新配置       # 修改已有配置
```

---

## ⏰ 定时任务（HEARTBEAT）

skill 支持 OpenClaw 的 cron 定时推送，复制以下命令配置：

```bash
# 盘前简报 — 周一到周五 6:50 AM EST
openclaw cron add \
  --name "pre-market-brief" \
  --cron "50 11 * * 1-5" \
  --session isolated \
  --message "运行 trading-journal-sync 的 Pre-Market Brief" \
  --announce

# 收盘复盘提醒 — 周一到周五 4:15 PM EST
openclaw cron add \
  --name "post-market-nudge" \
  --cron "15 21 * * 1-5" \
  --session isolated \
  --message "运行 trading-journal-sync 的 Post-Market Nudge" \
  --announce

# 周度Playbook — 每周日 7:00 PM EST
openclaw cron add \
  --name "weekly-playbook" \
  --cron "0 0 * * 1" \
  --session isolated \
  --message "运行 trading-journal-sync 的 Weekly Playbook" \
  --announce
```

---

## 📁 文件结构

```
trading-journal-sync/
├── SKILL.md                         # 主控逻辑 + 模块路由
└── references/
    ├── notion-schema.md             # Notion 数据库 schema + 写入规则
    ├── setup-guide.md               # 首次配置对话式引导
    └── analysis-rules.md            # 分析规则 + Streak + Weekly Playbook
```

---

## 🎯 适配的 Notion 模板

本 skill 默认适配 **Ultra Trader Pro** 交易模板的数据库结构，包括：

- 交易记录（支持 relation 关联：账户、策略、品种、错误类型、交易周期）
- 复盘日历（日/周/月/年复盘）
- 策略分析
- 品种分析
- 错误分析
- 账户仪表盘

**也支持自定义 Notion 数据库** — 首次配置时 skill 会自动读取你的 schema 并建立字段映射。

---

## 🔒 隐私与安全

- 所有数据仅在你的 OpenClaw 实例和 Notion 之间传输
- 不收集、不存储、不上传任何交易数据到第三方
- 截图仅用于本地 OCR 识别，不外传
- 配置信息存储在你自己的 `USER.md` 中

---

## 🤝 反馈与贡献

- **Bug / 功能建议**: [GitHub Issues](https://github.com/Rever3l/Finlo-Notion-Trading-Analysis/issues)
- **社群**: 加入 Finlo 交易模板用户群交流使用心得

---

## 📄 License

MIT

---

*Built by [Finlo](https://github.com/Rever3l) — Notion 交易系统模板*
