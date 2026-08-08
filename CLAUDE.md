# CLAUDE.md

本文件为 Claude Code 在此仓库工作时的项目指引。

## 项目简介

王陆《807雅思词汇》在线听写练习工具。纯静态页面，部署在 GitHub Pages：
https://sammi-code-cloud.github.io/-807/

## 目录结构约定

```
english\
├── index.html          # 章节路由导航页（首页），chapterList 数组登记全部章节
├── html\               # 各章节听写页面，命名：chapter章节号(起始页-结束页).html
├── words\              # 单词原始数据，按章节分文件夹：words\chapterN\chapterN(页码).txt，每行一个单词
├── progress\
│   └── progress.md     # 导入进度记录表
├── prompt\
│   ├── 提示词模板.md    # 可复用生成提示词
│   └── 原始需求-勿删.md # 听写页面功能的原始需求（硬性交互规则以此为准）
```

## 核心工作流：新增章节听写页面

用户往 `words\chapterN\` 放入 txt（每行一个英文单词）后，要求生成听写页面。必须完成以下四步：

1. **生成页面**：模仿 `html\chapter7(43-44).html` 生成同名 html 到 `html\` 目录
   - txt 只含单词，需自动补全：phonetic（英式音标）、zh（中文释义，含词性）、tip（雅思听写考点：拼写陷阱/易混词/场景/复数考点）
   - 单词数据放 JS 数组 `wordListRaw`，每项 `{"w":"单词","phonetic":"音标","zh":"释义","tip":"提示","page":页数}`（page 为数字页码；无法逐词区分时用字符串如 "43-44"）
   - 页面顶部黄色说明块需总结本章拼写陷阱分类（双写字母/不发音字母/复数/易混词/派生词）
2. **登记路由**：在 `index.html` 的 `chapterList` 数组追加 `{chapter, pages, file, count}`
3. **登记进度**：在 `progress\progress.md` 导入进度总览表追加一行（含导入日期）
4. **提交推送**：用户确认后 `git push`，GitHub Pages 自动更新

## 听写页面硬性规则（详见 prompt\原始需求-勿删.md）

- 发音：谷歌词典牛津真人美音 mp3 直链（禁止浏览器 TTS）
  `https://ssl.gstatic.com/dictionary/static/sounds/oxford/{单词}--_us_1.mp3`
  失败回退：us_1 → us_2 → 复数词回退单数形式
- 交互：回车提交判分；**答错不跳题**，错误信息（用户输入+标准答案+tip）停留页面，再按回车才切下一题；答对同样需回车切题
- 统计：进度、正确数、错误数、正确率实时更新
- 导出：xlsx-js-style CDN 导出带样式 xlsx，**随时可导出（无需学完）**，未作答单词一并导出且判分列为 IF 公式（Excel 内补填答案自动判分）；列为 序号/标准答案/音标/中文释义/考点提示/你的答案/判分/🔊发音(mp3超链接)/页数；首行大标题合并居中，表头蓝底白字，样式对齐 `excel\807单词听写练习表-第七章.xlsx`；末尾加汇总行，设置列宽
- 单文件 HTML，CSS/JS 内嵌，仅 CDN 外部依赖，电脑手机自适应
- 页面提示文案：引导用 GitHub Pages 或 http 服务访问，不要以本地 python 服务为主要方式

## 命名与用词注意

- 统一用 **chapter**（曾误写为 charter，已全部改正，勿再引入）
- 文件命名：`chapterN(起始页-结束页)`，如 `chapter7(41-42)`
- Excel 导出文件名：`ChapterN(页码)听写练习结果.xlsx`

## 部署

- GitHub 仓库：https://github.com/Sammi-code-cloud/-807 （main 分支根目录部署 Pages）
- 页面全部用相对路径（`html/文件名`），保证 Pages 子路径下可用
- `.gitignore` 忽略 `*.xlsx`（练习导出产物）；words 目录必须入库（原始数据备份）
