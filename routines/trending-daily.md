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
4. 再写一句"今日观察"，总结当天热榜的整体倾向（例如某类工具集中上榜）。
5. 套用仓库根目录下 `_template.html` 的样式和结构，把数据填进去，生成一份新的 HTML。
   热度条按"今日新增 star"相对第 1 名归一化计算宽度（`style="width:NN%"`）。
6. 保存为 `reports/YYYY-MM-DD.html`（用今天的日期），并更新 `reports/index.html`，
   在 `<!-- LATEST -->` 标记下方加一条指向今天这份的链接（最新在最上方）。
7. 提交改动并推送（提交信息：「trending 日报 + 日期」）。

注意：如果抓取失败或不足 10 个项目，在 HTML 顶部明确标注数据可能不完整（保留模板里的
`.warning` 节点并写明原因），不要编造仓库或数字。

## 索引插入格式

在 `reports/index.html` 的 `<!-- LATEST -->` 之后插入：

```html
<li><a href="YYYY-MM-DD.html">GitHub Trending · YYYY-MM-DD</a><span class="when">前 10 名</span></li>
```

首次生成时记得删除列表里的「暂无日报」占位 `<li>`。
