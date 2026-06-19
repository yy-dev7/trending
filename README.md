# 每日技术日报

由 Claude routines（定时任务）每天自动生成两份日报，发布到 GitHub Pages：

- **GitHub Trending 日报** — 当日热门开源项目前 10 名，含中文功能解读与热度趋势。
- **行业动态日报** — 行业博客与科技人物的最新动态，中文要点汇总。

## 目录结构

```
.
├── index.html              # 首页 / 导航
├── assets/style.css        # 共享样式
├── _template.html          # Trending 日报模板
├── _template_news.html     # 行业动态模板
├── reports/                # Trending 日报板块
│   ├── index.html          #   归档列表（新日报插到最上方）
│   └── YYYY-MM-DD.html     #   每日一份
├── news/                   # 行业动态板块
│   ├── index.html          #   归档列表
│   └── YYYY-MM-DD.html     #   每日一份
├── routines/               # 定时任务提示词（喂给 /schedule）
│   ├── trending-daily.md
│   └── industry-news.md
└── .nojekyll               # 让 GitHub Pages 跳过 Jekyll，原样发布 HTML
```

## 定时任务（routines）

`routines/` 目录存放两个任务的提示词。在 Claude Code 里用 `/schedule` 创建定时
cloud agent，把对应 `.md` 里「任务」整段作为指令，设定每天执行即可。任务会自行抓取
数据、套用模板生成 HTML、更新索引并提交推送。

## 启用 GitHub Pages

仓库已配置远程 `git@github.com:yy-dev7/trending.git`。首次推送后，到
**仓库 Settings → Pages**，将 Source 设为 `main` 分支、根目录 `/`，保存。
页面地址通常为 `https://yy-dev7.github.io/trending/`。

## 本地预览

直接用浏览器打开 `index.html`，或在仓库根目录起一个静态服务器：

```bash
python3 -m http.server 8000
# 然后访问 http://localhost:8000/
```
