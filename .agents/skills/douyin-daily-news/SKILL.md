---
name: douyin-daily-news
description: 从得到大脑的“抖音”知识库完整获取运行日 T-3 的视频原文，生成按日 AI 新闻总结，并更新仓库 GitHub Pages 首页。适用于每日运行、补跑或重跑本仓库日报；不负责创建或修改定时任务。
---

# 抖音 AI 日报更新

在本仓库根目录执行完整的日报更新。使用官方 `getnote` CLI 读取数据；不要创建 Codex 自动化、系统计划任务、GitHub Actions，也不要提交或推送 Git。

## 固定配置

- 知识库名称：`抖音`
- 知识库范围：`DEFAULT`
- 时区：`Asia/Shanghai`
- 目标日期：运行日减 3 个自然日（T-3）
- 原文目录：`source-text/<target-date>/`
- 总结文件：`summary/<target-date>.md`
- 展示页面：`index.html` 中 `id="daily-news-data"` 的 JSON 数据块

用户明确给出“运行日”时以该日期计算 T-3；否则读取当前 `Asia/Shanghai` 日期。不要把 T-3 解释为过去 72 小时。执行前先明确报告运行日和目标日期。

## 1. 预检

1. 确认当前目录包含本文件、`source-text/README.md`、`summary/README.md` 和 `index.html`。
2. 运行 `getnote kbs --scope DEFAULT -o json`。所有 CLI 调用都要求退出码为 0 且 JSON `success=true`。
3. 按 CLI 当前 `--help` 支持的分页参数翻完知识库列表。只接受名称恰好为 `抖音`、scope 为 `DEFAULT` 的唯一结果，并保留返回的字符串 `topic_id`。找不到或同名多条时停止，不猜 ID。
4. 使用临时工作目录收集和校验本次结果。未完成全量校验前，不覆盖当日正式目录、总结或首页。

不要把凭据、Cookie、访问令牌或完整 CLI 响应写入仓库。

## 2. 枚举目标日的全部视频

1. 运行 `getnote kb bloggers <topic_id> -o json`，按返回的 `has_more` 和 CLI 帮助中的分页参数读完所有页。保存每位博主的字符串 `follow_id_str`、`account_name` 和 `platform`。
2. 对每位博主运行 `getnote kb blogger-contents <topic_id> <follow_id> -o json`，翻完所有页。除非接口明确保证排序和停止条件，否则不能因为遇到早于目标日的视频就提前停止分页。
3. 将 `post_publish_time` 按 `Asia/Shanghai` 解释并筛选日历日期等于目标日期的条目。时间字段无法可靠解析时停止并报告对应博主与 `post_id_alias`。
4. 以字符串 `post_id_alias` 去重。相同 ID 的标题或发布时间发生冲突时停止，不能任意选一条。
5. 对每个目标视频运行 `getnote kb blogger-content <topic_id> <post_id> -o json`。以详情返回的 `post_publish_time` 再次核验目标日期，并读取 `post_title`、`post_name`、`post_media_text`、`post_url`。标题优先使用非空 `post_title`，其次使用非空 `post_name`，最后回退到列表中的 `post_title`；三者都为空时写“未命名视频”。
6. `post_media_text` 才是完整原文。字段缺失或为空时本次运行失败，不能使用 `post_summary`、列表摘要或模型补写。

任一列表分页、详情请求或业务校验失败时，本次运行不完整。报告失败命令、`error.message/reason`、`retryable` 和可选 `request_id`；不要继续发布总结或首页。所有分页都成功且筛选结果为 0 条时，视为有效的空日报。

## 3. 写入原文和清单

阅读 `source-text/README.md` 并严格遵守其中的文件与清单格式。

1. 每条视频写为 `<HHmmss>--<post_id_alias>.md`。只替换 Windows 文件名禁止字符，ID 始终按字符串处理。
2. YAML 中记录标题、博主、发布时间、来源 URL、`topic_id`、`follow_id`、`post_id` 和抓取时间。
3. `## 原始文本` 后逐字保存 `post_media_text`；只规范化换行并补文件末尾换行。
4. 为每个文件计算 SHA-256，生成按 `publishedAt`、`postId` 稳定排序的 `manifest.json`。
5. 校验详情成功数、唯一 ID 数、清单条目数和清单所列文件数完全相等，且每个 SHA-256 与文件一致；之后才把 `complete` 写为 `true`。
6. 重跑时覆盖同 ID 的既有生成文件。只清理上一份 `manifest.json` 明确列出、但本次全量结果已不存在的旧生成文件；不得删除未被旧清单管理的用户文件。

所有文件使用 UTF-8。正式目录发布完成后，再继续生成总结。

## 4. 生成每日总结

阅读 `summary/README.md`，然后只读取当日 `complete: true` 的清单及其列出的原文。

1. 为每条视频提炼标题、博主、发布时间、内容摘要和“为什么值得关注”。
2. 合并重复观点形成 3 至 8 条重点动态；视频不足时允许少于 3 条，不凑数。
3. 只在至少两条独立原文共同支持时写趋势判断，并说明支撑来源；证据不足就明确写“未形成可验证趋势”。
4. 不补造原文没有提到的公司、模型版本、发布日期、融资额、排名或因果关系。必要信息缺失时写“原文未说明”。
5. `## 视频摘要` 必须覆盖 manifest 的每一条视频。0 条时明确写“当日未发现视频”。
6. 写入 `summary/<target-date>.md`，并核验日报中的视频条目数等于 manifest 的 `videoCount`。

## 5. 更新 GitHub Pages 首页

先解析 `index.html` 中当前 `daily-news-data.targetDate`。当前日期为空、等于本次目标日期或早于本次目标日期时才更新首页；本次目标日期更早时属于历史补跑，只写原文和总结，保留首页的较新日报。

需要更新时，只编辑 `index.html` 内 `<script type="application/json" id="daily-news-data">` 的 JSON 内容，保留其余 HTML、CSS 和 JavaScript 结构。写入：

```json
{
  "targetDate": "YYYY-MM-DD",
  "generatedAt": "带 +08:00 的 ISO 8601 时间",
  "sourceCount": 0,
  "headline": "当日一句话标题",
  "overview": "今日速览",
  "highlights": [
    { "tag": "主题", "title": "重点标题", "summary": "重点说明" }
  ],
  "items": [
    {
      "title": "视频标题",
      "creator": "博主名称",
      "publishedAt": "带 +08:00 的 ISO 8601 时间",
      "summary": "内容摘要",
      "whyItMatters": "为什么值得关注",
      "sourceUrl": "原始来源 URL；没有则为空字符串"
    }
  ]
}
```

`sourceCount`、`items.length` 和 manifest 的 `videoCount` 必须相等。0 条日报使用空数组，并把 headline 设为“当日未发现视频”。所有内容通过 JSON 编码，不把原始文本直接拼成 HTML。写入 script 数据块前把 JSON 字符串中的 `<` 转义为 `\u003c`，避免内容中的 `</script>` 提前结束数据块。更新后确认 JSON 可解析，并在桌面与移动宽度检查页面没有溢出、遮挡或脚本错误。

## 6. 完成判定

只有下列条件全部成立才报告成功：

- 当日 manifest 存在且 `complete=true`；
- manifest 数量、文件数量、唯一 ID 数和 SHA-256 全部一致；
- 日报视频条目覆盖 manifest 全部视频；
- 本次应更新首页时，首页 JSON 可解析，且 `sourceCount`、`items.length` 与 manifest 一致；
- 本次应更新首页时，首页展示的 `targetDate` 是本次目标日期；历史补跑时，首页仍展示原有较新日期。

最终报告目标日期、视频数量、原文目录、总结文件和首页更新状态。失败时保留上一次成功首页，不用部分结果覆盖它。
