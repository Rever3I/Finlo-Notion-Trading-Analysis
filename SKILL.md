---
name: trading-journal-sync
description: 交易日志同步助手，帮助Notion交易模板用户通过截图、Excel/CSV或自然语言快速录入交易记录，并提供交易分析、复盘提醒、streak激励和周度策略报告。当用户发送交易截图、说"记录交易"/"录入"/"log trade"、要求复盘/"review"、查看streak、请求交易分析/"pattern"/"分析我的交易"、或说"周报"/"weekly playbook"时触发。也在用户说"设置交易日志"/"setup journal"时触发首次配置流程。即使用户只是发了一张券商截图不说话，也应该触发此skill。
---

# Trading Journal Sync 交易日志同步助手

为Notion交易模板用户提供完整的交易记录管理和分析系统。支持OpenClaw定时推送。

## 核心能力

1. **智能录入** — 截图识别 / Excel导入 / 自然语言录入
2. **交易分析** — Pattern recognition / 错误归因 / 绩效统计
3. **复盘系统** — 收盘提醒 / 结构化复盘 / 截图归档
4. **Streak激励** — 连续复盘追踪 / 里程碑提醒
5. **Weekly Playbook** — 周度策略复盘 / 下周计划

---

## Mode Detection 模式路由

根据用户输入自动判断执行模式：

| 触发条件 | Mode | 参考文件 |
|---------|------|---------|
| 发送截图/图片 | **Screenshot Import** | `references/notion-schema.md` |
| 发送CSV/Excel文件 | **File Import** | `references/notion-schema.md` |
| "记录交易"/"log" + 交易信息 | **NL Import** | `references/notion-schema.md` |
| "setup"/"设置"/"初始化" | **First-Time Setup** | `references/setup-guide.md` |
| "复盘"/"review" | **Trading Review** | `references/notion-schema.md` |
| "streak"/"连续" | **Streak Check** | `references/analysis-rules.md` |
| "分析"/"pattern"/"统计" | **Pattern Analysis** | `references/analysis-rules.md` |
| "周报"/"weekly playbook" | **Weekly Playbook** | `references/analysis-rules.md` |
| HEARTBEAT 盘前触发 | **Pre-Market Brief** | `references/analysis-rules.md` |
| HEARTBEAT 收盘后触发 | **Post-Market Nudge** | `references/notion-schema.md` |

**执行前务必先读取对应的 reference 文件。**

---

## Step 0: 检查用户配置

每次触发时，先检查用户是否已完成首次配置。

配置信息存储位置：
- **OpenClaw**: USER.md 中的 `[trading-journal-config]` 段落
- **Claude Code**: 项目根目录的 `trading-config.md` 文件
- 配置内容包括：数据库URL、券商类型、各relation页面映射、时区

**如果未配置** → 自动切换到 Setup 模式，读取 `references/setup-guide.md`。

---

## Module 1: 智能录入

### Screenshot Import 截图识别

1. **识别券商**: 根据UI特征判断 Robinhood/IBKR/Webull/其他
2. **提取数据**: Ticker, Side, Entry/Exit, Quantity, Date, Commission
3. **展示确认**: 结构化展示识别结果，让用户确认或修正
4. **智能补充**: 询问截图中无法获取的字段（策略、账户等）
   - 展示用户已有的选项供选择
5. **写入Notion**: 
   - 查找对应的 relation 页面
   - 创建交易记录条目
   - 截图嵌入页面正文作为归档
6. **确认**: 返回写入成功的Notion链接

读取 `references/notion-schema.md` 获取字段映射和写入规则。

### File Import CSV/Excel导入

1. **解析文件**: 读取表头
2. **字段映射**: 自动映射 + 用户确认
3. **数据预览**: 前5条确认
4. **批量写入**: 逐条写入，处理relation
5. **汇总**: 成功/失败统计

### Natural Language Import 自然语言录入

解析如 "今天做多NVDA 112股 175入 180出" 的自然语言，同截图流程。

---

## Module 2: 交易分析

读取 `references/analysis-rules.md` 获取完整规则。

### Pattern Recognition

从Notion读取历史交易，分析维度：
- 按策略/品种/周期/方向的胜率和P&L
- 按时间段分析（早盘/午盘/尾盘）
- 错误类型频率和成本
- 连续盈亏streak
- 亏损后行为模式（revenge trading检测）

输出：核心发现（最多3条）+ 数据支撑 + 改进建议

### Pre-Market Brief

**HEARTBEAT**: 每个交易日 6:50 AM EST

- 当前持仓状态
- 今日到期期权
- 基于历史pattern的注意事项
- Pre-trade checklist

---

## Module 3: 复盘系统

### Post-Market Nudge

**HEARTBEAT**: 每个交易日 4:15 PM EST

推送：当日交易摘要 + P&L汇总 + "该复盘了！"

### Structured Review 结构化复盘

1. 读取当日交易记录
2. 引导用户回答：执行了什么setup / 情绪状态 / 最佳最差交易 / 改进点
3. 在复盘日历创建 "日复盘" 条目，关联当日交易记录
4. 用户回答写入页面正文
5. 更新streak

### Screenshot Archive 截图归档

交易录入时发送的截图 → 嵌入交易记录页面正文 → 复盘时可回看

---

## Module 4: Streak 激励

读取 `references/analysis-rules.md` Streak部分。

- 从复盘日历读取日复盘记录
- 仅工作日计算（跳过周末/休市日）
- 每次复盘后显示：🔥 Streak天数 + 本月复盘率

里程碑：7 / 14 / 21 / 30 / 50 / 100 / 365天

---

## Module 5: Weekly Playbook

读取 `references/analysis-rules.md` Weekly部分。

**HEARTBEAT**: 每周日 7:00 PM EST

内容：本周总结 / Best&Worst Trade / 错误统计 / 策略表现 / streak状态 / 下周关注事件 / 行动项

输出到复盘日历，类型="周复盘"。

---

## OpenClaw HEARTBEAT 配置

```bash
# 盘前简报 — 周一到周五 6:50 AM EST
openclaw cron add --name "pre-market-brief" --cron "50 11 * * 1-5" --session isolated --message "运行 trading-journal-sync 的 Pre-Market Brief" --announce

# 收盘提醒 — 周一到周五 4:15 PM EST
openclaw cron add --name "post-market-nudge" --cron "15 21 * * 1-5" --session isolated --message "运行 trading-journal-sync 的 Post-Market Nudge" --announce

# 周度Playbook — 每周日 7:00 PM EST
openclaw cron add --name "weekly-playbook" --cron "0 0 * * 1" --session isolated --message "运行 trading-journal-sync 的 Weekly Playbook" --announce
```

## 语言

中文为主，术语保留英文（P&L, R-multiple, streak, pattern, setup）。
