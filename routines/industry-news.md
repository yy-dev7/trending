# Routine：行业动态日报

> 定时任务提示词，配合 Claude Code 的 `/schedule`（routines）使用，建议每天执行一次。

## 任务

每天为我汇总一份科技 / AI 行业动态日报。

步骤：
1. 浏览主流行业博客与科技人物的最新动态，来源可包括但不限于：
   - 官方博客：OpenAI、Anthropic、Google DeepMind、Meta AI、Hugging Face 等
   - 资讯/聚合：Hacker News 热榜、TechCrunch、The Verge、Ars Technica
   - 人物动态：知名研究者 / 创始人 / 工程师的公开发言与文章
2. 选出当天 6–10 条最值得关注的动态。对每条提取：标题/主题、来源、可点击的原始链接、
   分类（如「模型发布」「融资」「开源」「政策」）。
3. 用简体中文转述每条要点（2–3 句），并写明"为什么值得关注"，不要照抄原文。
4. 写一句"今日观察"，总结当天行业动态的整体趋势。
5. 套用仓库根目录下 `_template_news.html` 的样式和结构，生成新的 HTML。
6. 保存为 `news/YYYY-MM-DD.html`（今天的日期），并更新 `news/index.html`，
   在 `<!-- LATEST -->` 标记下方加一条指向今天这份的链接（最新在最上方）。
7. 提交改动并推送（提交信息：「行业动态 + 日期」）。

注意：每条动态都必须给出真实可点击的来源链接，**不要编造链接、事实或数字**。
来源不足或不确定时，在 HTML 顶部保留 `.dc-warning` 节点并说明原因。

## 索引插入格式

在 `news/index.html` 的 `<!-- LATEST -->` 之后插入：

```html
<li><a href="YYYY-MM-DD.html">行业动态 · YYYY-MM-DD</a><span class="when">N 条</span></li>
```

首次生成时记得删除列表里的「暂无动态」占位 `<li>`。
