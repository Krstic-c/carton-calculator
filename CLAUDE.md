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

2026-09-12 起改为苹果风格（之前是黑白工业工程图美学，历史版本见 git log / CHANGELOG）。
对应 `~/.claude/skills/design-style/references/apple-minimal.md`；这个项目和 ID Studio Asia
品牌矩阵（黑白工程图风格）不再是同一套视觉语言，是刻意的独立选择。

- **整体风格**：克制极简、大留白、圆角卡片 + 柔和阴影，用间距/阴影而不是粗黑框做分隔
- **字体**：中英文统一系统字体栈 `"PingFang SC", -apple-system, BlinkMacSystemFont, "Helvetica Neue", Arial, sans-serif`（无外部字体加载，纯离线可用）；数字用 `font-variant-numeric: tabular-nums` 对齐，不用单独等宽字体
- **强调色**：只用一个暖红 `#e14b39`（深色模式下 `#ff6b52`），不引入其他彩色
- **色板/圆角/阴影**：CSS 变量集中定义在 `index.html` 的 `:root`（`--paper`/`--paper-2`/`--ink`/`--ink-dim`/`--accent`/`--radius-card`(20px)/`--radius-ctrl`(10px)/`--shadow-card`），深色模式通过 `@media (prefers-color-scheme: dark)` 覆盖同一批变量，JS 生成的等轴视图 SVG 直接引用这些变量（`var(--accent)` 等），改配色只需改 `:root`，不用碰 JS
- **交互控件**：勾选框做成真实的 iOS 风格滑动开关（`.switch`），装载方式切换用分段控件（`.toggle-group`），不用原生 checkbox/radio 直接摆着不管样式
- 以后新增页面/组件，先对照这份规范，不要自作主张换风格

## 已知的坑

- STEP01 推荐外箱算法：不能只按"体积利用率"排序，会选出像 1×1×23 这种细长条箱（数学上利用率最高，但完全不实用）。
  已加长宽高比例过滤（优先长宽高比 ≤3 的紧凑方案，找不到再放宽到 5/8/不限），排序前先按比例过滤再比体积利用率。
- 等轴视图（isometric）超过 260 个格子时会自动降级为"网格分隔线"展示（不再画实心方块），避免 SVG 元素过多卡顿。
- 排列组合搜索允许 a×b×c ≥ N（不要求整除 N），多出来的格位视为"空位"，在等轴图里画成虚线空心方块。
- 卡板/货柜底面排列支持"横竖混排"（`bestMixedLayout`）：不再强制整层同一个方向，会尝试把剩余空间切成两块、用旋转90°的方向填补边，取数量最多的方案。目前只支持两块（主区+补边区），不做完整二维装箱最优解。
- 外箱侧放/倒放（`bestOrientedLayout`，STEP02/STEP03地板散装的"允许外箱侧放/倒放"勾选框）：采用启发式近似，不追求数学最优——对整批外箱统一尝试 3 种"哪条边朝上"的摆放方式（每种方式下底面仍会跑 `bestMixedLayout` 做横竖混排），取总数量最多的一种，而不是给每个箱子单独找最优朝向（那是 NP-hard 问题，专业装柜软件也是走近似算法）。默认不勾选，行为与未加此功能前完全一致。
- STEP03"货柜内允许卡板堆叠"：按 `货柜内高 ÷ 卡板堆高` 自动算层数，只是数学上能放得下，不代表底层货物包装真的能承重，UI 已加提示但没做承重强度校验（本来也没法算，纸箱抗压看具体材质）。

## 待办事项

（暂无，如有新需求再补充）
