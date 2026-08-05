# 装箱计算器 Carton / Pallet / Container Loading Calculator

单文件本地网页工具，用于：
1. 彩盒尺寸 → 推荐外箱尺寸（自动遍历排列组合与摆放方向，按体积利用率排序）
2. 外箱 → 卡板装载利用率（每层箱数、层数、堆高/载重限制）
3. 卡板/外箱 → 货柜装载利用率（20GP/40GP/40HQ/45HQ，支持按卡板或地板散装两种模式）

全部计算在浏览器本地完成，不上传任何数据，无需安装依赖。

## 本地使用
直接双击 `index.html` 用浏览器打开即可。

## 在线访问（GitHub Pages）
仓库设置 Pages 后，访问：
`https://<你的GitHub用户名>.github.io/carton-calculator/`

## 技术说明
纯 HTML + CSS + 原生 JS 单文件实现，无构建步骤、无第三方依赖（除 Google Fonts CDN）。
