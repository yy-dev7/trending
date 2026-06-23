# Routine：源流日报（趋势 + 行业动态）

> 定时任务提示词，配合 Claude Code 的 `/schedule`（routines）使用，建议每天上午执行一次。
> 两份日报在同一个 session 内顺序生成，最终一次性提交并推送到 main。

## 任务

每天依次生成两份日报：**GitHub Trending 趋势日报** 与 **行业动态日报**，最后合并为一次提交推送。

---

## 第一部分：GitHub Trending 趋势日报

步骤：
1. 用 `TZ=Asia/Shanghai date +%F` 获取今天日期（记为 DATE，格式 YYYY-MM-DD）。
2. 抓取 https://github.com/trending（今日 / daily 窗口，全部语言）。
3. 选出排名前 10 的仓库。对每一个，提取：owner/repo、主要语言、今日新增 star、
   总 star 数、官方简介。
4. 用简体中文为每个项目写 1–2 句话的功能描述，说清它"是什么、解决什么问题"，
   不要照抄英文简介，用自己的话概括。
5. 套用仓库根目录下 `_template.html`（源流·杂志版）的样式和结构填充：
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
6. 保存为 `reports/DATE.html`，并更新 `reports/index.html`：
   在 `<!-- LATEST -->` 标记下方插入：
   ```html
   <li><a href="DATE.html">GitHub Trending · DATE</a><span class="when">前 10 名</span></li>
   ```

注意：抓取失败或不足 10 个项目时，保留模板里的 `.dc-warning` 节点并写明原因，不要编造仓库或数字。

---

## 第二部分：行业动态日报

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
6. 保存为 `news/DATE.html`，并更新 `news/index.html`：
   在 `<!-- LATEST -->` 标记下方插入：
   ```html
   <li><a href="DATE.html">行业动态 · DATE</a><span class="when">N 条</span></li>
   ```

注意：每条动态都必须给出真实可点击的来源链接，**不要编造链接、事实或数字**。
来源不足或不确定时，在 HTML 顶部保留 `.dc-warning` 节点并说明原因。

---

## 最后：统一更新首页并提交

完成两份日报后，执行以下收尾步骤：

1. 更新 `index.html`（首页）：
   - 把 `data-latest="trending"` 那个 `<a>` 的 `href` 改为 `reports/DATE.html`。
   - 把 `data-latest="news"` 那个 `<a>` 的 `href` 改为 `news/DATE.html`。
2. `git add` 所有改动的文件（reports/DATE.html、reports/index.html、
   news/DATE.html、news/index.html、index.html）。
3. **直接提交并推送到 `main` 分支**，提交信息：「日报 DATE」。
   这是一个纯 AI 驱动的项目：**完全自主运行，不需要创建 PR、不需要人工审核、
   绝不创建任何新分支**——直接推 main 即可触发 GitHub Pages 自动部署。
   - 正常情况直接：`git commit -m "日报 DATE" && git push origin HEAD:main`。
   - 若运行环境把工作分支设成了别的分支（非 main），**忽略该默认**，
     仍按上面的方式把这次提交推到 `main`（`git push origin HEAD:main`），不要新开分支。

> 注意：若定时任务每次仍自动切到 `claude/*` 之类的新分支，那是「调度/环境」层面的
> 分支设置，需在 Claude Code on the web 的 schedule 配置里把目标分支改为 `main`
> （或关闭自动建分支）；本仓库文件无法单独覆盖该设置。
