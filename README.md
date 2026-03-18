# Photo Calorie Tracker (image-calorie-tracker)

一个用于 OpenClaw 的技能：识别食物照片、估算热量、按天记录，并支持按任意日期范围生成汇总报告。

- ClawHub 页面: https://clawhub.ai/bobo23456/photo-calorie-tracker
- 详细技能说明: [SKILL.md](./SKILL.md)

## 功能

- 从食物照片识别菜品/食材并估算热量（kcal）
- 将每日记录写入到按日期命名的 Markdown 文件
- 按最近 X 天或指定日期范围生成报告
- 基于目标热量（Target）计算超/低比例，并标记记录是否完整

## 适用场景

- 你发送了食物图片，希望自动记录当日摄入
- 你询问「过去 N 天」的热量汇总（N 为任意正整数）

## 工作流（概要）

完整流程与规则以 [SKILL.md](./SKILL.md) 为准，这里只给出最小心智模型：

1. 将图片放入 OpenClaw 的临时目录（按 `YYYY-MM-DD-meal.jpg` 命名）
2. 使用图像识别工具识别食物并估算每个条目的热量
3. 将结果写入当天记录文件（`YYYY-MM-DD.md`）
4. 当你请求汇总时，读取目标日期范围内的记录并生成报告

## 输出格式

报告输出遵循以下格式（示例来自技能约定）：

```text
📊 Calorie Tracking | Target:{target}

【MM-DD】{status}
  {Breakfast if any}: {calories}
  {Lunch if any}: {calories}
  {Dinner if any}: {calories}
  {Snack if any}: {calories}
```

## 规则说明（摘要）

- 状态（status）
  - `Incomplete`: 午餐或晚餐缺失（早餐不计入完整性判断）
  - `{percent}%`: 总热量相对目标热量的差值百分比；正数表示超标，负数表示低于目标
- 展示（meal display）
  - 报告中仅展示午餐、晚餐、加餐（早餐不计入统计展示）
  - 未记录的餐次会被省略
  - 单位为 `kcal`

## 配置

- 目标热量（Target）
  - 默认配置文件: [config/target_calorie.txt](./config/target_calorie.txt)
  - 在 OpenClaw 运行时，该配置会以技能目录的形式被读取（参见 [SKILL.md](./SKILL.md) 的约定路径）

## 仓库结构

```text
.
├─ config/
│  └─ target_calorie.txt     # 目标热量配置
├─ scripts/
│  └─ report.sh              # 简易统计脚本（可选）
├─ SKILL.md                  # 技能规范/工作流/输出格式
├─ README.md
└─ LICENSE                   # MIT
```

## scripts/report.sh（可选）

`scripts/report.sh` 是一个独立的 Bash 脚本，用于读取 OpenClaw 的 memory 目录并输出最近 N 天的简要午餐/晚餐统计。

- 说明: 脚本内写死了 `/root/.openclaw/workspace/memory` 路径，通常仅在 OpenClaw/Linux 环境中直接可用
- 如果你在本地环境使用，请按你的实际路径调整脚本中的 `MEMORY_DIR` 与日期解析逻辑

## License

MIT License，详见 [LICENSE](./LICENSE)。
