<div align="center">

<img src="assets/logo.svg" alt="douyin-AI-news" width="440">

**每天 3 分钟，读完抖音上值得看的 AI 视频。**

[![在线预览](https://img.shields.io/badge/在线预览-GitHub%20Pages-4c1?style=flat-square&logo=github&logoColor=white)](https://lihaosen-222.github.io/douyin-AI-news/)
[![更新频率](https://img.shields.io/badge/更新频率-每日-0969da?style=flat-square)](#更新节奏)
[![技术栈](https://img.shields.io/badge/技术栈-HTML%20%2F%20CSS%20%2F%20JS-e34c26?style=flat-square&logo=html5&logoColor=white)](#技术栈)

</div>

---

## 目录

- [项目简介](#项目简介)
- [功能特性](#功能特性)
- [快速开始](#快速开始)
- [内容规范](#内容规范)
- [目录结构](#目录结构)
- [维护指南](#维护指南)
- [更新节奏](#更新节奏)
- [技术栈](#技术栈)
- [订阅博主](#订阅博主)
- [路线图](#路线图)
- [声明](#声明)

---

## 项目简介

[douyin-AI-news](https://github.com/lihaosen-222/douyin-AI-news) 从订阅的抖音 AI 博主内容中筛选指定日期发布的视频，保留原始资料，并将其整理为结构化的新闻摘要，最终发布为一个**无需后端即可访问的静态网页**。

它的重点不是简单罗列视频，而是回答每条内容的四个问题：

| 问题 | 关注点 |
| :--- | :--- |
| **用了什么** | 涉及的模型、工具、硬件或工作流 |
| **在做什么** | 视频中实际执行的任务或操作 |
| **做出什么** | 得到的结果、数据或可交付成果 |
| **当前状态** | 正式发布、开源、官方演示、个人实践或待验证 |

---

## 功能特性

- **事件级速览** —— 顶部「今日 30 秒速览」按事件合并同源信息，同一事件只出现一次，并标注事件数与信源数。
- **结构化摘要** —— 每条视频固定输出四要素，避免"看标题猜内容"。
- **原文可核对** —— 折叠区保留视频原始文本，摘要与原文一一对应。
- **按日归档** —— 每日内容独立存为 `source/YYYY-MM-DD.md`，可追溯、可 diff。
- **移动端友好** —— 响应式布局，手机与桌面均可阅读。
- **零依赖构建** —— 纯静态页面，直接推送即可完成发布。

---

## 快速开始

### 作为读者

直接打开在线版本即可，无需任何安装：

```text
https://lihaosen-222.github.io/douyin-AI-news/
```

### 作为维护者

```bash
git clone https://github.com/lihaosen-222/douyin-AI-news.git
cd douyin-AI-news

# 本地查看（任选一种）
# 方式一：直接双击打开
start index.html          # Windows
open index.html           # macOS

# 方式二：起一个本地服务
python -m http.server 8000
```

然后打开 <http://localhost:8000> 预览。

---

## 内容规范

`index.html` 中的内容遵循以下规则：

1. 页面顶部显示日期、视频数量与「今日 30 秒速览」。
2. 速览按**事件**总结而非按视频逐条总结，并标明事件数与信源数。
3. 速览只描述具体事实 —— 发生了什么、正在做什么、取得了什么结果；**不写缺乏依据的趋势判断**。
4. 每条视频必须包含 `用了什么`、`在做什么`、`做出什么`、`当前状态` 四项摘要。
5. `当前状态` 需区分正式发布、开源、官方演示、个人实践与待验证内容；**资料未提及的信息不得猜测**。
6. 保留博主、发布时间、原视频链接、关键词与可折叠的原始文本。
7. 页面按发布时间排列，并确保手机与桌面端均可清晰阅读。

> 原始整理文件的格式说明见 [`source/README.md`](source/README.md)。

---

## 目录结构

```text
douyin-AI-news/
├── assets/
│   └── logo.svg            # 项目 Logo
├── source/                 # 按日期归档的原始整理内容
│   ├── README.md           # 原始文件格式说明
│   ├── 2026-09-05.md
│   ├── 2026-09-06.md
│   └── 2026-09-07.md
├── index.html              # 最新一期新闻页面
├── index.example.html      # 页面示例模板
└── README.md
```

---

## 维护指南

更新一期内容的完整流程：

1. 从「得到」的「抖音」知识库中查找**目标日期**发布的视频。
2. 将整理后的原始内容保存到 `source/YYYY-MM-DD.md`。
3. 依据原始内容更新 `index.html`（结构参照 `index.example.html`）。
4. 检查摘要、原文、链接以及移动端显示效果。
5. 提交并推送变更，由 GitHub Pages 自动发布。

<details>
<summary><strong>目标日期如何计算？</strong></summary>

目标日期 = **当前日期 − 3 天**。

例如当前日期为 2026 年 9 月 8 日，则整理目标日期为 2026 年 9 月 5 日，对应文件为 `source/2026-09-05.md`。

留出 3 天缓冲，是为了等待视频的完整文案、评论与后续更正稳定下来。

</details>

---

## 更新节奏

- **频率**：每日一期。
- **口径**：整理「今天 − 3 天」当天发布的视频。
- **发布**：推送到 `main` 分支后由 GitHub Pages 自动生效，通常一分钟内可见。

---

## 技术栈

| 项目 | 说明 |
| :--- | :--- |
| 页面 | 原生 HTML + CSS + JavaScript，无框架、无构建步骤 |
| 数据 | 内嵌于 `index.html` 的 JSON，随页面一起版本化 |
| 托管 | GitHub Pages |
| 来源 | 「得到」抖音知识库 |

---

## 订阅博主

<details>
<summary><strong>展开全部 20 位博主</strong></summary>

| 博主 | 链接 |
| :--- | :--- |
| AI课代表小明 | <https://v.douyin.com/26oUrR8wgCM/> |
| 羊叨叨AI | <https://v.douyin.com/CHFH25kGYRs/> |
| 怼怼教ai | <https://v.douyin.com/d_qZKn2-WIM/> |
| 秋芝2046 | <https://v.douyin.com/kaOhLfqyAOY/> |
| Git源宝 | <https://v.douyin.com/0Q3h1XkIl0I/> |
| AI便利峰 | <https://v.douyin.com/8BOR2h03SK4/> |
| 清华姜学长 | <https://v.douyin.com/J9eUzZaDyV0/> |
| 海洋AI笔记 | <https://v.douyin.com/pC7JlOUHf7c/> |
| 木子不写代码 | <https://v.douyin.com/maNQdO66T90/> |
| 数字游牧人Samuel | <https://v.douyin.com/9VEufyz2w7g/> |
| 小天fotos | <https://v.douyin.com/DPkqZJhwcCk/> |
| 晓辉博士 | <https://v.douyin.com/GBu4-5_hxAs/> |
| 脑三聊AI | <https://v.douyin.com/_lmRtH2MRCA/> |
| 瘦继痕迹 | <https://v.douyin.com/goyjUoxL5Zk/> |
| 西门聪明蛋XD | <https://v.douyin.com/atlgpx3mZKY/> |
| 第四种黑猩猩 | <https://v.douyin.com/dKRY0F5FxYA/> |
| 卡卡大王（AI版） | <https://v.douyin.com/5CUHxFUDZp0/> |
| 技术爬爬虾 | <https://v.douyin.com/lPo_ZhtS4B8/> |
| 柱子哥TzFilm | <https://v.douyin.com/wVIQ9lu2FYs/> |
| 我是王青青 | <https://v.douyin.com/xBZkch323hw/> |

</details>



---

## 声明

- 本站为**信息整理与索引**项目，视频内容的著作权归原作者及平台所有。
- 摘要力求忠实于原始视频，如与原文存在出入，请以原视频为准。
- 若涉及内容授权问题，请通过 [Issue](https://github.com/lihaosen-222/douyin-AI-news/issues) 联系，将及时处理。

---

<div align="center">

用 ❤️ 与 ☕ 维护 · 如果觉得有用，欢迎点个 ⭐

</div>
