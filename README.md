# A-lioth.github.io

一个纯静态的「请假详情」页面生成工具：先在 `login.html` 中填写请假表单，数据存入浏览器 `localStorage`，随后跳转到 `index.html`，由脚本把填写内容注入到一份静态的「请假详情」页面模板中，用于在本地查看 / 截图 / 演示一张完整的请假审批详情页。

## 技术栈

- **语言**：HTML5 + CSS3 + 原生 JavaScript（ES6，浏览器端）
- **页面框架**：`index.html` 引入了 Vue 2 运行时（`请假详情_files/vue.min.js`），模板本身是已渲染好的静态 DOM
- **UI 风格**：Vant 风格组件类名（`van-cell`、`van-field`、`van-button`、`van-uploader` 等）与校园平台自定义样式（`xg-*` 前缀），样式集中在 `请假详情_files/style.css`
- **第三方资源**：`请假详情_files/xg-res.min.js`（压缩的页面运行时，`index.html` 末尾调用 `MOB_INIT.initPage()` 初始化页面）
- **构建工具**：无。仓库内没有 `package.json` / `pom.xml` / `build.gradle` / `requirements.txt` / `pyproject.toml`，无打包、无依赖安装步骤
- **数据存储**：浏览器 `localStorage`（键 `access_granted`、`form_data`），不涉及后端或数据库

## 目录结构

```
A-lioth.github.io/
├── index.html          # 请假详情页面（读取 localStorage 数据并注入）
├── login.html          # 请假信息填写页（表单 + 校验 + 数据落盘）
├── 请假详情_files/      # 详情页依赖的静态资源
│   ├── style.css       # 页面样式（含 Vant / xg-* 组件样式）
│   ├── vue.min.js      # Vue 2 运行时
│   └── xg-res.min.js   # 校园平台页面运行时（压缩）
└── README.md
```

## 主要功能 / 模块说明

| 文件 | 模块 | 说明 |
| --- | --- | --- |
| `login.html` | 表单录入 | 学生基本信息（学号、姓名、院系、专业、班级、现在年级）、请假信息（性质、起止时间、事由、天数、是否出校）、审核信息（发起时间、审核人、审核时间）以及手机号、学年、学期、请假类型、来源类型等扩展字段 |
| `login.html` | 时间格式校验 | 对 `approveDateTime` / `startTime` / `endTime` 校验 `YYYY-MM-DD HH:mm`，对 `submitTime` / `auditTime` 校验带秒的 `YYYY-MM-DD HH:mm:ss`，不合法时弹出遮罩提示 |
| `login.html` | 请假天数自动计算 | 监听开始/结束时间变化，按 `(结束 - 开始) / 一天` 计算并保留两位小数，结果回填到「请假天数」输入框 |
| `login.html` | 默认值填充 | 打开页面时把所有 `datetime-local` 字段默认设为当前时间 |
| `login.html` | 访问口令 | 「神秘代码」固定为 `123`，校验通过后将 `access_granted=true` 与 `form_data`（JSON）写入 `localStorage`，再 `window.location.replace('index.html')` |
| `index.html` | 访问拦截 | 页面加载时检查 `localStorage.access_granted`，未通过则跳回 `login.html` 并终止后续脚本 |
| `index.html` | 数据注入 | 按标签文本精确匹配 `.van-cell`，把 `form_data` 写入「基本信息」「请假信息」各字段，并更新顶部「已审核通过」提示与审核流程中的发起/审核时间、审核人 |
| `index.html` | 隐藏字段展开 | 修复「展开 / 收起」按钮：切换 `.hiddenField` 区域的显示，并同步箭头图标与按钮文字 |
| `index.html` | 退出清理 | `beforeunload` 时删除 `access_granted` 与 `form_data`，避免数据残留 |

页面模板中的「请假详情」展示区包含：审核通过提示条、基本信息、请假信息（含可展开的手机号 / 学年 / 学期 / 请假类型 / 来源类型 / 证明材料）、销假信息、审核流程（发起申请 → 待班导师审核）、底部「拍照定位销假 / 续假申请」按钮。

## 运行 / 构建方式

无需安装依赖、无需构建，直接静态打开即可：

1. 用浏览器打开仓库根目录的 `login.html`（或通过任意静态服务器访问，例如在根目录执行 `python -m http.server 8000` 后访问 `http://localhost:8000/login.html`）。
2. 填写表单（神秘代码填 `123`）并提交，页面会携带数据跳转到 `index.html` 查看生成的详情页。
3. 由于 `index.html` 依赖 `localStorage` 中的 `access_granted`，直接打开 `index.html` 会被重定向到 `login.html`。

> 若部署到 GitHub Pages，需通过 `https://<用户名>.github.io/A-lioth.github.io/login.html` 进入，而不是直接访问详情页。

## 备注

- 这是个人自用的练习 / 学习型仓库，不是可直接上线的产品；没有后端、没有数据库、没有测试与 CI。
- `index.html` 的页面结构与 `请假详情_files/` 下的 `style.css`、`xg-res.min.js`、`vue.min.js` 来自校园统一平台的页面快照（第三方代码，非本项目原创），本仓库的改动主要是在其上追加数据注入、校验与交互脚本。
- 页面模板与 `login.html` 中保留了示例个人信息（学号、姓名、手机号、审核人等），仅用于本地演示，请勿提交真实个人数据到公开仓库。
- 「神秘代码」为前端硬编码校验，仅用于挡住直接访问，不能作为真实的安全措施。
- 未提供任何自动化构建、部署或测试命令；如需补充，待完善。
