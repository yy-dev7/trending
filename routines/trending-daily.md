# Routine：GitHub Trending 日报

> 这是一个定时任务的提示词（prompt）。配合 Claude Code 的 `/schedule`（routines）使用，
> 建议每天上午执行一次。把下面「任务」整段作为 routine 的指令。

## 任务

每天为我生成一份 GitHub Trending 日报。

步骤：
1. 抓取 https://github.com/trending（今日 / daily 窗口，全部语言）。
2. 选出排名前 10 的仓库。对每一个，提取：owner/repo、主要语言、今日新增 star、
   总 star 数、官方简介。
3. 用简体中文为每个项目写 1–2 句话的功能描述，说清它"是什么、解决什么问题"，
   不要照抄英文简介，用你自己的话概括。
4. 套用仓库根目录下 `_template.html`（源流·杂志版）的样式和结构填充：
   - HERO 的 `LEAD_DECK`：一句引言概述当日榜单看点。
   - FEATURE（焦点项目）= 第 1 名：填 owner/repo、2–3 句中文说明、总 star、今日新增。
   - 焦点项目配图 `LEAD_IMG`：优先取该仓库 README 的代表性图片——
     · 抓 raw README（试 `main`/`master` 或读默认分支），**扫描整个正文**提取 `![](…)` 与
       `<img src>`（不要只看开头几张，很多 README 开头全是徽章、真图在后面）；
     · **过滤掉徽章**（URL 含 `shields.io` / `badge` / `flat-square` 等）；
     · 相对路径（如 `docs/x.png`）转 `https://raw.githubusercontent.com/{owner}/{repo}/{分支}/{路径}`；
     · 取过滤后第一张真图作为 `LEAD_IMG`。
     · README 没有真图时，`LEAD_IMG` 填 GitHub OG 卡片 `https://opengraph.githubassets.com/0/{owner}/{repo}`
       （模板已内置 OG→头像两级兜底，故 LEAD_IMG 填 README 真图或 OG 卡片均可）。
   - `PULL_QUOTE`：一句点睛的「今日观察」金句。
   - RANKING（今日榜单）= 第 2–10 名，共 9 行，按今日新增 star 降序。
   - 「趋势观察」写 3 段（壹/贰/叁）展开分析，总结当天热榜的整体倾向。
   - 顶栏 `DATE_DOT` 用点分日期（如 2026.06.19）。
5. 保存为 `reports/YYYY-MM-DD.html`（用今天的日期），并更新两处索引：
   - `reports/index.html`：在 `<!-- LATEST -->` 标记下方加一条指向今天这份的链接（最新在最上方）。
   - `index.html`（首页）：把 `data-latest="trending"` 那个 `<a>` 的 `href`
     改成 `reports/YYYY-MM-DD.html`（即首页「阅读今日」直达当天报告）。
6. 提交改动并推送（提交信息：「trending 日报 + 日期」）。

注意：如果抓取失败或不足 10 个项目，在 HTML 顶部明确标注数据可能不完整（保留模板里的
`.dc-warning` 节点并写明原因），不要编造仓库或数字。

## 索引插入格式

在 `reports/index.html` 的 `<!-- LATEST -->` 之后插入：

```html
<li><a href="YYYY-MM-DD.html">GitHub Trending · YYYY-MM-DD</a><span class="when">前 10 名</span></li>
```

首次生成时记得删除列表里的「暂无日报」占位 `<li>`。
