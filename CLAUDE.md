# 装箱计算器 项目说明

## 技术架构总览

纯前端单文件工具，无构建步骤、无后端、无第三方 JS 依赖，全部计算在浏览器本地完成。

## 文件结构

- `index.html` — 主文件，GitHub Pages 入口
- `carton-pallet-calculator.html` — 与 index.html 内容一致的备份/打包件
- `README.md` — 项目说明
- `.gitignore` — 忽略 `.DS_Store`、`.claude/`、`node_modules/`

## 部署流程

1. 改完 `index.html` 后同步更新 `carton-pallet-calculator.html`（保持两者一致）
2. `git add` / `commit`，展示 diff 给用户确认
3. 用户确认后 `git push origin main`
4. GitHub Pages（Deploy from branch: main / root）自动重新部署，通常 1-2 分钟生效

访问链接：https://krstic-c.github.io/carton-calculator/
仓库地址：https://github.com/Krstic-c/carton-calculator

## 视觉设计规范

- **整体风格**：黑白工业工程图美学，克制、专业。不用渐变、阴影、圆角卡片这类"互联网范"装饰
- **字体**：
  - 标题/正文：DM Sans
  - 数字/代码/技术细节：DM Mono
  - 中文：Noto Sans SC
- **强调色**：只用一个红色 `#b23a2e` 作为唯一点缀色，不引入其他彩色
- **版式参考**：借鉴技术图纸/工程图纸的登记表、修订记录表排版逻辑（例如博客首页 "REV 编号 + title block" 的设计思路）
- 以后新增页面/组件，先对照这份规范，不要自作主张换风格

## 已知的坑

（暂无）

## 待办事项

- 按上述视觉规范重新设计 `index.html` 界面
