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

## 国际化（中文 / English）

2026-10-12 新增。页面右上角一个分段控件（`#langToggle`，中文/EN）切换全站语言，包括**已经算出来的结果**（表格、读数卡片、等轴图标注、警示文案、弹窗），不需要用户重新点一次「计算」。

- **文案全部集中在 `const I18N = {zh:{...}, en:{...}}`**（在 `<script>` 顶部、state 变量之后），两个对象结构完全对称、key 一一对应。值可以是普通字符串，也可以是函数（需要插值数字/子文案时，比如 `rcLayoutSub: (cap,n) => \`格位 ${cap} 个，装 ${n} 个彩盒\``）；取值统一走 `t(key, ...args)`，不要在业务代码里直接拼接中/英文字符串。新增任何用户可见文案，都要同时写 zh 和 en 两份，不能只写一种语言就合并。
- **静态 HTML 文案**：打了 `data-i18n`（纯文本，`el.textContent=`）/ `data-i18n-html`（含 `<b>` 等标签，`el.innerHTML=`）/ `data-i18n-ph`（placeholder）三种属性，`applyStaticI18n()` 统一扫描应用。品牌名"装箱计算器"和 Summary 区标题"汇总链路 Summary Chain"是刻意保留的双语固定文案，不随语言切换（类似 logo，不翻译），没有打 `data-i18n`。
- **`setLang(lang)` 是语言切换的唯一入口**：切语言时**不能**简单粗暴地重新跑 `calcOuterOptions()`/`calcPallet()`/`calcContainer()` 完事——`calcOuterOptions()` 会把 STEP01 的选中项重置回第一名，如果用户之前手动点了排名靠后的某一行，语言一切就会丢失这个选择（真实踩过，测试时发现的）。正确做法：STEP01 用 `renderOuterOptions()` 重新画表格 + 用已存的 `selectedOuterOption` 重新调 `selectOuterOption()`（只重绘，不重选）；STEP02 的 `renderPalletResult()` 本来就是纯函数（从全局 `palletResult` 读数据画 UI，不重新计算），直接调用最安全；STEP03 没有拆出等价的纯渲染函数，`calcContainer()` 本身是纯函数（只读当前输入和 `containerMode`，无隐藏状态副作用），重新调用是安全的，但如果以后 STEP03 也要支持"保留某种用户手选状态"，必须先照 STEP01/02 的模式拆出一个纯渲染函数，不能再偷懒直接重调 `calcContainer()`。
- **预设下拉框的 value 碰撞坑**（测试语言切换时顺手发现，**和 i18n 无关，是更早就有的 pre-existing bug**，这次没有动它）：`makePresetManager.saveCurrent()` 存自定义预设后用 `select.value = "l,w"` 去选中新选项，如果用户新存的预设尺寸恰好和某个内置预设完全相同（比如卡板存了 1200×1000，和内置的"亚洲标准"选项 value 一样），`<select>.value=` 会命中 DOM 里**第一个**匹配该 value 的 `<option>`（内置的那个），导致新存的自定义预设没有真正被选中，此时点"删除当前预设"会提示"当前选中的不是自定义预设"。复现条件很窄（新预设尺寸要跟某个内置预设完全一致），线上应该很少被踩到，如果以后要修，思路是给每个预设 option 一个独立的 `data-preset-idx`、选中逻辑用索引不要用 value 字符串匹配。
- **`.toggle-group button{flex:1}` 在"两个按钮文案长度差很多"时不稳**：装载方式那种两个中文按钮（"按卡板装载"/"地板散装（无卡板）"）用 `flex:1` 等分没问题，但语言切换控件是"中文"（2字）配"EN"（2个拉丁字母但视觉窄很多），active 按钮会变 `font-weight:600`，这个加粗会轻微改变 flex 分配，在某些宽度下把非激活按钮的文案挤到换行（实测截图发现过）。给 `#langToggle button` 单独覆盖成 `flex:none` 解决（按内容自身宽度排布，不受兄弟按钮宽度变化影响），没有改全局的 `.toggle-group button` 规则（怕影响装载方式那个正常工作的地方）。

## 已知的坑

- STEP01「放尺 / 整体余量」（`margin_mm`）**不是单独的"纸板厚度"字段**——代码里只有这一个数，`calcOuterOptions()` 对每个维度只加一次（`a*e0 + margin`，不是两侧分别加）。默认值 8mm（2026-10-12 从 4mm 调整）是"两侧纸板厚度合计（约 6-8mm，按常见单坑瓦楞纸板 3-4mm/层估算）+ 装箱间隙"的通用估算，不是单纯的插入间隙。算出来的 `dimX/dimY/dimZ` 会直接填进 STEP02 的"外箱（Outer Carton）"输入框，当作外箱的**外部物理尺寸**参与卡板/货柜装载计算，所以这个余量取小了会导致外箱尺寸偏小、下游装载计算跟着偏乐观。用户如果用的是特殊材质（如双坑瓦楞、EVA 内衬等），应自行调大这个数字。STEP01 结果区（`drawOuterDiagram()`）同时展示"内尺寸（彩盒净尺寸，= a*e0/b*e1/c*e2，不含余量）"和"预计外尺寸"两张卡片，并在下方加了说明文字标注两者差值就是余量、且结果非精确出图尺寸，下单前要和供应商确认——这是为了不让用户误以为算出来的尺寸可以直接下单生产。
- **`.readout .rvalue` 长数值会溢出卡片边界的坑**（2026-10-12 用户截图发现）：STEP01 从 3 张卡片变成 4 张后，`.readout-grid`（`minmax(130px,1fr)`）分给每张卡片的宽度变窄，"408×368×328"这种 11 字符的数字在 26px 字号下的真实渲染宽度（~169px，用 Range/canvas measureText 量过）比卡片内容区（~126px）宽，而数字和"×"之间没有可换行的断点，默认 `overflow:visible` 下文字直接从卡片右边界溢出、糊到下一张卡片上——**`getBoundingClientRect()` 量 `.rvalue` 元素本身不会发现这个问题**（量出来的 width 只有 126px，看着"没有溢出"），因为元素被 flex/grid 布局挤到了那个宽度，溢出的是"画出来的文字"而不是"元素的盒子"；要验证必须用 `Range.getBoundingClientRect()` 或 `scrollWidth` 看文字的真实渲染宽度，或者直接截图肉眼看。修复：`.rvalue` 加了 `overflow-wrap:anywhere` 兜底（真遇到更长的值就换行而不是溢出），另外给长数字类卡片加了 `.rvalue.dims{font-size:19px}` 缩小字号、减少触发换行的频率；`readoutCard()` 新增第 5 个可选参数 `valueClass` 来挂这个 class，其余十几处调用不传就不受影响。以后再往 `readoutCard` 里塞长数值（尤其是数字+×拼接、没有空格的字符串），记得检查窄屏下是否溢出，不要只看桌面宽屏。
- STEP01 推荐外箱算法：不能只按"体积利用率"排序，会选出像 1×1×23 这种细长条箱（数学上利用率最高，但完全不实用）。
  已加长宽高比例过滤（优先长宽高比 ≤3 的紧凑方案，找不到再放宽到 5/8/不限），排序前先按比例过滤再比体积利用率。
- 等轴视图（isometric）超过 260 个格子时会自动降级为"网格分隔线"展示（不再画实心方块），避免 SVG 元素过多卡顿。
- 排列组合搜索允许 a×b×c ≥ N（不要求整除 N），多出来的格位视为"空位"，在等轴图里画成虚线空心方块。
- 卡板/货柜底面排列支持"横竖混排"（`bestMixedLayout`）：不再强制整层同一个方向，会尝试把剩余空间切成两块、用旋转90°的方向填补边，取数量最多的方案。目前只支持两块（主区+补边区），不做完整二维装箱最优解。
- 外箱侧放/倒放（`bestOrientedLayout`，STEP02/STEP03地板散装的"允许外箱侧放/倒放"勾选框）：采用启发式近似，不追求数学最优——对整批外箱统一尝试 3 种"哪条边朝上"的摆放方式（每种方式下底面仍会跑 `bestMixedLayout` 做横竖混排），取总数量最多的一种，而不是给每个箱子单独找最优朝向（那是 NP-hard 问题，专业装柜软件也是走近似算法）。默认不勾选，行为与未加此功能前完全一致。
- STEP03"货柜内允许卡板堆叠"：按 `货柜内高 ÷ 卡板堆高` 自动算层数，只是数学上能放得下，不代表底层货物包装真的能承重，UI 已加提示但没做承重强度校验（本来也没法算，纸箱抗压看具体材质）。

## 待办事项

（暂无，如有新需求再补充）
