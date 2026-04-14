# Trading Journal Sync 交易日志同步助手

为 Notion 交易模板用户提供完整的交易记录管理和分析系统。

## 核心能力

1. **智能录入** — 截图识别 / Excel导入 / 自然语言录入
2. **交易分析** — Pattern recognition / 错误归因 / 绩效统计
3. **复盘系统** — 结构化复盘 / 截图归档
4. **Streak激励** — 连续复盘追踪 / 里程碑提醒
5. **Weekly Playbook** — 周度策略复盘 / 下周计划

## 依赖工具

本项目依赖以下 MCP Server，使用前请确认已配置：
- **Notion MCP Server** — 用于读写 Notion 数据库（搜索、创建页面、更新页面、获取数据库 schema）

## 自动检测与模式路由

当用户发送消息时，根据以下规则自动判断执行模式：

| 触发条件 | 执行操作 | 对应命令 |
|---------|---------|---------|
| 发送截图/图片 | 截图识别录入 | `/project:log-trade` |
| 发送 CSV/Excel 文件 | 文件导入 | `/project:log-trade` |
| "记录交易"/"log"/"录入" + 交易信息 | 自然语言录入 | `/project:log-trade` |
| "setup"/"设置"/"初始化"/"设置交易日志" | 首次配置 | `/project:setup` |
| "复盘"/"review" | 结构化复盘 | `/project:review` |
| "streak"/"连续" | Streak 查询 | `/project:streak` |
| "分析"/"pattern"/"统计"/"分析我的交易" | 模式分析 | `/project:analyze` |
| "周报"/"weekly playbook" | 周度报告 | `/project:weekly-playbook` |
| "盘前"/"pre-market" | 盘前简报 | `/project:pre-market` |
| "收盘"/"post-market" | 收盘提醒 | `/project:post-market` |

**执行任何模块前，务必先读取对应的 reference 文件。**

## Step 0: 检查用户配置

每次触发时，先检查项目根目录是否存在 `trading-config.md` 文件。

配置内容包括：数据库 URL、券商类型、各 relation 页面映射、时区、字段映射。

**如果配置文件不存在或内容不完整** → 自动切换到 Setup 模式，读取 `references/setup-guide.md` 执行配置流程。配置完成后将信息保存到 `trading-config.md`。

## 参考文件

执行各模块时，读取以下参考文件获取详细规则：

- `references/notion-schema.md` — Notion 数据库 schema + 写入规则
- `references/setup-guide.md` — 首次配置引导流程
- `references/analysis-rules.md` — 分析规则 + Streak + Weekly Playbook

## 语言

中文为主，术语保留英文（P&L, R-multiple, streak, pattern, setup）。
