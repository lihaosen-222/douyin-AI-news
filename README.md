# 抖音 AI 日报

这个仓库保存「得到大脑」中 `抖音` 知识库的视频原文，并基于原文生成按日归档的 AI 新闻摘要。仓库根目录的 `index.html` 始终展示最近一次成功生成的日报，可直接部署到 GitHub Pages。

## 日期口径

每次运行处理 **运行日之前第 3 个自然日（T-3）** 的视频，时区固定为 `Asia/Shanghai`。

例如：任务在 `2026-09-08` 运行，目标日期是 `2026-09-05`。这里按自然日计算，不是“过去 72 小时”。

## 仓库结构

```text
.
|-- .agents/skills/douyin-daily-news/
|   `-- SKILL.md              每日任务必须执行的完整流程
|-- source-text/
|   |-- README.md             原文与清单格式
|   `-- YYYY-MM-DD/           某日的全部视频原文及 manifest.json
|-- summary/
|   |-- README.md             日报格式
|   `-- YYYY-MM-DD.md         某日总结
|-- index.html                GitHub Pages 首页，只展示最新成功日报
`-- README.md
```

日期目录和日报文件只有在对应日期完成全量获取后才算有效。上游查询、分页或正文获取有任何失败时，不得用部分数据更新 `summary/` 或首页。

## 运行前准备

运行环境需要：

- 已安装并登录官方 `getnote` CLI；
- 当前账号能够读取默认空间中名称恰好为 `抖音` 的知识库；
- 能够读取该知识库的博主列表、视频列表和视频详情；
- 在仓库根目录运行 Codex，使仓库内 Skill 可以被发现。

先用下面的命令检查连接；它只读取数据：

```powershell
getnote kbs --scope DEFAULT -o json
```

如果机器上尚未完成得到大脑授权，请先单独完成登录和权限配置。仓库不会保存账号凭据、Cookie 或访问令牌。

## 每日执行

本项目没有创建或附带任何定时任务。手动运行或配置你自己的自动化时，让 Codex 在仓库根目录执行：

```text
使用 $douyin-daily-news 更新今天应处理的抖音 AI 日报。
```

如需补跑，可明确提供运行日；Skill 仍会计算该运行日的 T-3：

```text
使用 $douyin-daily-news，以 2026-09-08 为运行日补跑日报。
```

一次成功运行会：

1. 将 `抖音` 知识库中目标日期的全部视频原文写入 `source-text/YYYY-MM-DD/`；
2. 生成 `source-text/YYYY-MM-DD/manifest.json`，记录全量核验结果；
3. 仅依据这些原文生成 `summary/YYYY-MM-DD.md`；
4. 更新 `index.html` 内的 `daily-news-data`，使首页显示这份最新日报。

重复运行同一日期是幂等的：相同视频使用相同文件名，更新已有生成文件，不产生重复副本。

