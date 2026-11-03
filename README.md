# weekly-report-skill

一个用于把多日日报、工作日志或钉钉日志导出内容整理成结构化周报的 Codex Skill。

核心目标是忠实整理，不是润色成“漂亮话”：保留原始事实、动态识别项目、跨日合并同一事项、谨慎汇总数量，并输出可直接粘贴到钉钉日志表单的纯文本。

当前版本使用“复制日志并粘贴”的方式，不需要钉钉日志 API 审批；以后审批通过后，也可以直接输入 `fetch_my_logs.py` 导出的 JSON。

## 能力

- 接收粘贴文本、Markdown、文件路径或钉钉日志导出的 JSON。
- 自动识别日期、项目、工作事项、状态、数量和后续计划。
- 跨日合并连续进展，按业务链而不是日期组织。
- 仅对口径一致、互不重叠的新增量做合计。
- 将口语转成专业表达，但禁止夸大完成度、责任或成果。
- 动态生成业务模块，不把具体项目写死在 Skill 中。
- 默认输出钉钉粘贴版；需要时附审阅说明和待确认事项。

## 不做什么

- 不主动调用钉钉接口，不自动抓取日志。
- 不自动发送周报或消息。
- 不编造缺失的日期、项目、结果、风险和下周计划。
- 不把多人日志默认合并为一份团队周报。

## 安装

把仓库克隆到 Codex 的 skills 目录，并让文件夹名与 Skill 名一致：

```powershell
$skills = if ($env:CODEX_HOME) {
    Join-Path $env:CODEX_HOME 'skills'
} else {
    Join-Path $env:USERPROFILE '.codex\skills'
}

New-Item -ItemType Directory -Force -Path $skills | Out-Null
git clone https://github.com/Ceciliayyy/weekly-report-skill.git (Join-Path $skills 'weekly-report')
```

如果已经下载了仓库，也可以把整个目录复制到 `$CODEX_HOME/skills/weekly-report`。未设置 `CODEX_HOME` 时，对应 `C:\Users\<用户名>\.codex\skills\weekly-report`。

## 使用

在 Codex 中显式调用：

```text
使用 $weekly-report，把我下面粘贴的日报整理成本周周报。
周期：2026-10-27 至 2026-11-02。
按钉钉字段输出，不要编造缺失信息。
```

然后把日报或 JSON 内容粘贴进去。

推荐给每条日志加简单分隔，但格式不统一也可以先交给 Skill 处理：

```text
汇报周期：2026-10-27 至 2026-11-02

===== 2026-10-27 | 日报 | 项目：项目A =====
今日完成：……
明日计划：……
风险或需协调：……
```

## 输出

默认输出可直接复制到钉钉的纯文本，字段通常为：

- `汇报时间`
- `本周工作成果`
- `重要工作进展`
- `未完成事项&原因说明`
- `下周工作计划`
- `其他`

如果公司模板字段不同，以用户提供的模板为准。需要核对事实、数字或归类时，可以要求 Skill 同时输出审阅说明和待确认事项。

## 目录

```text
weekly-report-skill/
|-- SKILL.md
|-- agents/
|   `-- openai.yaml
`-- references/
    |-- input-normalization.md
    |-- output-format.md
    |-- examples.md
    `-- terminology.md
```

## 设计原则

1. 先建事实台账，再归并和写作，避免直接从原文跳到成品。
2. 业务模块动态生成，固定的是处理流程和输出字段，不是具体项目分类。
3. 术语升级必须保持原意；不确定时使用中性词并列出待确认。
4. 数量只有确认属于同一口径的独立新增量时才相加。
5. 默认只生成草稿，发送到钉钉前由用户核对。
