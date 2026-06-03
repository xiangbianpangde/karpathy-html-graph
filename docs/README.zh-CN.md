# 48h 学习链路图谱 · 离线便携包

> 适用于 AITutor V3.1 项目 · 单文件图谱 + 离线 cytoscape + teach-trace skill v2.0

## 包内文件

| 文件 | 大小 | 用途 |
|------|------|------|
| `48h-learning-graph.html` | ~54 KB | **主交付物** — 4 Tab 思维导图式图谱,双击浏览器即可打开 |
| `cytoscape.min.js` | ~373 KB | Cytoscape.js 3.30.2 本地副本(原 CDN 引用已改为相对路径) |
| `teach-trace.md` | ~17 KB | teach-trace skill v2.0 主文档(以后跑 /teach-trace 用) |
| `references/default-style.md` | ~6 KB | Claude/Anthropic 暖色调配色模板 |
| `references/tab-schemas.md` | ~9 KB | 4 Tab 布局模式 + 7 个 v2.0 pitfalls |

**总大小**:~460 KB(主要是 cytoscape.js,本体 HTML 才 54 KB)

## 使用方法

### 1. 离线打开主图谱(本机 / 其他电脑通用)

直接双击 `48h-learning-graph.html` 即可。**无需联网**,因为 cytoscape 已经在同目录。

> **重要**:HTML 和 `cytoscape.min.js` 必须在同一目录,不能只移动 HTML。
> 推荐整包复制到目标电脑,然后双击 HTML 打开。

### 2. 在其他电脑使用 teach-trace skill

如果想在新电脑上用 `/teach-trace` 命令,把这个包里的 `teach-trace.md` 和 `references/` 目录复制到新电脑的 Mavis skill 目录,通常是:
- Windows: `C:\Users\<user>\.mavis\skills\teach-trace\` (或项目级 `.reasonix\skills\teach-trace\`)
- Mac/Linux: `~/.mavis/skills/teach-trace/`

结构应该是:
```
teach-trace/
├── teach-trace.md
└── references/
    ├── default-style.md
    └── tab-schemas.md
```

### 3. 跨平台使用

HTML + JS 完全跨平台:
- Windows 10/11 — 任意现代浏览器(Edge/Chrome/Firefox)
- macOS — Safari/Chrome/Firefox
- Linux — Chrome/Firefox

**唯一要求**:浏览器需要支持 ES6(2020 年后所有主流浏览器都支持)。

## 4 Tab 速览

| Tab | 内容 | 节点 | 边 | 布局 |
|-----|------|------|-----|------|
| 1 · 学习能力图谱 | L1 业务能力 + 子能力 + 指标 | 25 | 28 | 思维导图(根中心+4 方向) |
| 2 ⭐ · 设计方法图谱 | L2 设计方法 + 关键规则 | 22 | 30 | 思维导图(5 核心方法五边形) |
| 3 ⭐⭐ · 概念→设计→代码 | L1+L2+L3 三层映射 | 22 | 26 | 3 列预设 |
| 4 · 认知旅程 (48h) | 时间锚点 + 状态 + 系统动作 | 33 | 38 | 时间线预设 |

**⭐⭐ Tab 3 是核心** — 第一次看建议从它开始,直接看到"业务概念 → 设计方法 → 代码模块"的三层映射。

## 视觉风格

- **配色**:Claude/Anthropic 暖奶油 + 5 色暖棕层次(详见 `references/default-style.md`)
- **字体**:Source Serif Pro 标题 + Inter 正文 + JetBrains Mono 代码
- **自动对比文字色**:深色块用白字,浅色块用深字(亮度阈值 0.55 自动切换)
- **布局**:思维导图,根中心,4 方向分支,无散乱

## 交互

- **左下角** — 类型筛选(可勾选/取消某类节点)
- **节点点击** — 右侧抽屉显示详情 + file:line 引用
- **滚轮** — 缩放
- **拖动** — 平移

## 故障排查

| 现象 | 原因 | 解决 |
|------|------|------|
| 双击 HTML 是空白/加载失败 | cytoscape.min.js 不在同目录 | 确保两个文件在同一文件夹 |
| 拖动/缩放不灵敏 | 浏览器版本太老 | 升级到 Edge 90+/Chrome 90+ |
| 文字看不清(深色块) | 已自适应,无需调整 | 实在不行,把节点背景透明度调高 |
| 想改颜色 | 编辑 HTML 顶部 `:root` CSS 变量 | 详见 `references/default-style.md` |
| 想换数据/加 Tab | 改 HTML 底部的 `T1_NODES` 等 JS 数组 | 详见 `references/tab-schemas.md` |

## 文件:line 引用说明

图谱所有 `file:line` 引用都指向**真实设计文档**(项目在设计阶段,`src/` 还没写):
- `AI-enabled learning-v1.0\docs\plan\design\07-文件框架\M-XXX\FF-M-XXX-*.md` — 模块文件框架
- `AI-enabled learning-v1.0\docs\plan\design\04-整体结构设计\ADR-AITutor-V3.1-20260602.md` — 架构决策
- `AI-enabled learning-v1.0\docs\plan\design\08-系统模拟运行\48h-冲刺学习流程-场景文档.md` — 48h 场景
- `ai-tutor\doc\48H-SIMULATION-v2.md` — MCP 原始 48h 模拟(更详细)

如果移动到新电脑,这些引用虽然存在但**打不开**——它们只是用来"看到原文在哪",不是超链接。需要的话在原项目里打开对应行号看。

## 版本信息

- 生成时间:2026-06-03
- AITutor V3.1 设计终版(CCI=0.988 + V4.0 CCI=0.986, gatePassed=true)
- 4 核心模块:M-009 教学编排 / M-010 费曼评分 / M-011 FSRS / M-012 阶段校准
- teach-trace skill:v2.0
- cytoscape.js:3.30.2(MIT 协议)

## 许可

- HTML / Markdown:本项目内部使用
- cytoscape.min.js:MIT(https://github.com/cytoscape/cytoscape.js)
