---
name: "yj-blog-cover"
description: "飞熊博客封面：WP 16:9 + 公众号 16:9。集成 ljg-card -i，隐藏 colophon，技术色调，6 步 SOP。"
---

# yj-blog-cover: 飞熊博客封面生成器

## 目标

专门为飞熊博客（east196.cn WordPress）和微信公众号生成封面图。集成 ljg-card -i 信息图模具最佳实践，沉淀 2026-06-21 这轮 6 方法对比 + 3 次重新设计累积的经验。

解决三个核心痛点：
1. ljg-card colophon 硬编码"李继刚"（飞熊博客不能用）
2. ljg-card 固定 1080xauto（不是公众号头图 16:9 / WP 博客 16:9 比例）
3. ljg-card 缺"博客场景"模板（每次都要从零写 HTML）

## 触发词

- "博客封面"、"做封面"、"给文章 X 做封面"、"east196.cn 封面"
- "公众号头图"、"公众号封面"、"微信头图"
- "X 篇文章封面"、"cover for post X"
- "yj cover"、"yj blog cover"

## 默认参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| 模具 | ljg-card -i（默认）/ PIL 手画（fallback）| 信息图模式默认；PIL 手画备选（详见「坑 7 PIL fallback 路径」，适用调研类封面 + minimax 文字失真场景）|
| 比例 | 16:9 (1920x1080) | 博客封面 / WP featured 标准 |
| 备选比例 | 16:9 (1080x610) | 公众号头图标准（如需） |
| 调色 | 技术 (--bg #F0F3F7, --green #8EAAB8, --pink #C84A3B) | 工程师感硬核极简 |
| 字体 | DM Serif Display + DM Sans + KingHwa_OldSong | 避 Inter |
| colophon | 隐藏 | 飞熊博客不要"李继刚" |
| 弹点 | ≤ 2 处 | 90/8/2 法则 |
| 字号张力 | ≥ 10:1 | 最大 / 最小元素比例 |

## 设计原则

### 🔴 核心：满版铺满（v1.1 强制规则）

**问题诊断（2026-07-11）**：3 列 `space-between` 布局只让元素贴顶/底，**列内中间会出现 300-500px 空白带**（实测占总高 28-48%）。视觉上"上下填满中间空"，不专业。

**硬规则 v1.1**：
1. **背景层必须填满 1080 高度**——大色块/几何/网格/渐变区，至少占画面 60% 面积
2. **每列必须 ≥ 3 个内容块**，3 个块均匀分布上/中/下，禁用 2 个块的 `space-between`
3. **中央必须有强锚点**——大字标题/大数字/主视觉图，占满中央 200-400px 高度区间
4. **边角加装饰元素**——左下/右上小标签、几何线条、数据点散布，消除空白

### 4 种满版样式（按内容选）

| 样式 | 适用场景 | 视觉特征 |
|------|----------|----------|
| **A. 深色几何块** | 技术/AI/CLI 工具 | 大色块（#1f2024）+ 几何拼贴 + 强对比 |
| **B. 点阵网格** | 数据/图表/性能 | 100-500 个圆点矩阵 + 中央标题 |
| **C. 渐变区填** | 设计/创意/品牌 | 多色渐变 + 几何透明叠加 |
| **D. 大背景图** | 产品/UI/截图 | 真实截图/示意图 + 文字覆盖 |

### 旧 v1.0 原则（已废弃，仅参考）

### 画面撑满
- ~~固定画布内主体必须占 85% 以上高度~~
- ~~用 `justify-content: space-between`~~

### 密度与张力
- 90/8/2 法则不变，但增加垂直张力
- 字号张力保持 **≥ 10:1**

### 非对称重心
- ~~3 列比例 0.9fr 1.5fr 0.85fr~~（v1.0 设计导致中间空）

## 6 步 SOP

### 1. 选文章
- 飞熊给：WP ID / URL / 文章标题
- 拉 WP API：`curl -s https://east196.cn/index.php/wp-json/wp/v2/posts/{ID}`
- 验证文章存在（status=publish）

### 2. 拉内容
提取 6 字段（基于 ljg-card mode-infograph.md 步骤 2.2）：
- **标题**（≤ 15 字）
- **副标题**（≤ 30 字一句话核心）
- **密度**：稀 / 中 / 密
- **结构**：单点 / 对比 / 层级 / 流程 / 辐射 / 并列
- **情绪**：沉思 / 锐利 / 温暖 / 技术
- **锚点**：画面最大元素是什么？放哪里？

### 3. 选模具
- 默认 ljg-card -i（信息图）
- 备选：ljg-card -l（长图，正文头图）/ ljg-card -b（大字，小红书）
- 飞熊可指定

### 4. 渲染
- 写 HTML 到 `/tmp/yj_cover_{name}.html`
- HTML 模板必须包含：
  - `.colophon { display: none !important; }` 隐藏李继刚
  - 固定 `width: 1920px; height: 1080px`（博客封面 16:9）
  - 或固定 `width: 1080px; height: 610px`（公众号 16:9）
- **主体元素必须占画面 85% 以上高度**，用 `space-between` 或绝对定位消除上下真空。
- 上下边距控制在 `40–56px` 以内。
- 调 capture.js：
  - 公众号：`node /home/east/.agents/skills/ljg-card/assets/capture.js /tmp/yj_cover_{name}.html ~/.cache/covers/yj_{name}.png 1080 610`
  - 博客封面：`... 1920 1080`
- **非 fullpage 模式**（不传第 5 个参数）

### 5. 自检清单（v1.1 强化）

#### 5.1 必检项（不通过 = 重做）
- [ ] **🔴 中间无空白带？** 扫垂直像素柱状图，y=300-800 区间每行非背景像素 ≥ 1.5%（脚本：`scripts/check_empty_band.py`）
- [ ] **🔴 满版铺满？** 背景层（色块/几何/网格/渐变）覆盖整个 1920x1080，无裸露白底
- [ ] **🔴 中央锚点？** y=400-700 区间必须有核心大字/大数字/主视觉图
- [ ] colophon 已隐藏？（grep "李继刚" 应该是空）
- [ ] 弹点色 ≤ 2 处？
- [ ] 最大元素 ≥ 10:1 张力？

#### 5.2 风格检项
- [ ] 比例匹配目标（16:9）？
- [ ] 调色与内容主题匹配？
- [ ] 没用 Inter 字体？
- [ ] 没用纯黑 #000？（用 #1f2024 / #2D2926 替代）
- [ ] 没用 AI 紫蓝渐变？

#### 5.3 📱 移动端可读性（v1.2 新增，强制）
**核心原则**：封面图 50%+ 用户在手机端（350-400px 宽）查看，桌面端堆砌的 8 层信息 95% 会变成像素噪点。

**硬规则**：
- [ ] **桌面端最小可读字号 ≥ 14px**（100% 缩放下）
- [ ] **装饰性文字 ≥ 11px**，< 11px 直接砍（不缩）
- [ ] **3 层信息原则**：主标题（最显眼）+ 1 个核心数字 + Logo
- [ ] **删除**：所有 < 10px 装饰英文字（// LABEL / YEAR / 装饰字母）
- [ ] **删除**：网络连线节点上的微标签（手机端全像素化）
- [ ] **删除**：长段落正文（手机端没人读封面图正文）
- [ ] **测试**：用 PIL.resize 模拟 400x225 / 350x197 缩略图，主标题 + 数字必须清晰可辨
- [ ] **歧义自检**：避免 `+17 SKILLS / 1 SKILL.MD` 这种语义冲突，改成 `+17 MORE / 1 SKILL.MD`

#### 5.4 历史禁区（v1.0 已废）
- ~~[ ] 主体元素占画面 85% 以上高度~~（v1.0 漏检，v1.1 用 5.1 替代）
- ~~[ ] 禁止 `justify-content: center` 做垂直分布~~（v1.0 弱规则）

### 6. 交付
- 默认保存 `~/.cache/covers/yj_{name}.png`
- 飞熊可指定：
  - **dry-run**：只渲染，不上传
  - **upload**：upload WP + 替换 featured_media（用 wp-publish.py --cover）
- 报告：路径 + 关键 prompt 参数 + 自检结果

## 已知坑（2026-06-21 累积）

### 坑 1：ljg-card colophon 硬编码"李继刚"
- **症状**：渲染出来的图左下角显示"李继刚 + 头像"
- **根因**：ljg-card 模板固定 colophon div，硬编码"李继刚"
- **修复**：CSS 加 `.colophon { display: none !important; }`
- **预防**：每次新模板默认加这一行

### 坑 2：ljg-card 固定 1080 宽 + auto 高度
- **症状**：capture.js fullpage 模式输出 1080xauto（高度由内容决定），不是 16:9
- **根因**：capture.js fullpage 自动按 body.scrollHeight 截
- **修复**：
  - HTML body 设固定 `width: 1920px; height: 1080px`（博客 16:9）
  - 或固定 `width: 1080px; height: 610px`（公众号 16:9）
  - capture.js 用非 fullpage 模式（不传第 5 个参数）
- **预防**：模板默认提供 2 套固定尺寸（1920x1080 博客 / 1080x610 公众号）

### 坑 3：minimax AI Latin 乱字
- **症状**：baoyu-cover-image / mmx-image 生成的图常带"乱字招牌"（如"KOHCRASTO"）
- **根因**：minimax/image-01 渲染 Latin 文字不可控
- **解决**：
  - **首选**：ljg-card（HTML 模板渲染，文字可控）
  - **兜底**：**PIL 手画**（`scripts/gen-{name}-cover.py`，文字零失真）—— 详见「坑 7 PIL fallback 路径」

### 坑 4：调色错配
- "减法清单 / 对比 / 批判" → 用"技术"或"锐利"色调（#8EAAB8 蓝绿 + #C84A3B 朱红）
- "温暖故事 / 人物" → 用"温暖"色调（米色 + 暖橙）
- "哲学 / 认知 / 本质" → 用"沉思"色调（米色 + 棕）
- **宁可用默认也不要错配**——错误色调比无色调更糟

### 坑 5：信息密度过高
- **症状**：7 件套列表在 1080x460 中间列用 15px 字号 → 移动端看不清
- **根因**：ljg-card 默认 1080 宽设计，对应 1080xauto 高度；改成 1080x460 后所有字号要等比缩
- **修复**：
  - 列表字号提到 18-20px
  - 或减少列表项数（精选 4-5 项）
  - 或换"非对称网格"避免单列

### 坑 6：3 等分卡片
- **症状**：3 列等宽并排显示卡片
- **根因**：AI 生成痕迹
- **修复**：用 `grid-template-columns: 1fr 1.5fr 0.8fr` 等非对称

### 坑 7：PIL 手画 fallback 路径（2026-08-29 沉淀 · v1.4）
- **症状**：调研类封面需要"克制 / 杂志 / 白皮书"调性，ljg-card 模板偏"产品落地页"风格；image_generate minimax/image-01 又常 Latin 乱码（招牌乱码 / stats 全部失真）
- **根因**：ljg-card 视觉太营销；minimax/image-01 渲染 Latin 文字不可控
- **解决**：**PIL 手画 fallback**（`scripts/gen-{name}-cover.py` 5 套模板）
  - **位置**：`scripts/gen-{name}-cover.py`，固定 1920×1080 + RGB，输出 `~/.cache/covers/yj_{name}_*.png`
  - **模板复用链**：gen-elementary-cover.py → gen-d3-cover.py → gen-temporal-cover.py → gen-prefect-cover.py → gen-pi-cover.py（5 套一脉相承，只改色 + 改 caps + 改 stats）
  - **改 3 参数复用**（不需要重写）：
    1. **主色 + 顶部 hex 色**（项目 brand，如 dbt 橙 #FF694A / D3 蓝 #137BCD / Temporal teal #00939B / Prefect forest #08504C / Pi indigo #5A4FCF）
    2. **6 个 hex 容器 caps**（核心能力关键词，2 行×3 列）
    3. **底部 3 anchor stats**（stars / license / 最新版日期）
  - **固定结构**（不动）：顶部项目 hex + 主标题 140pt + 副标题 + 6 hex 容器 2 行×3 列 + 底部 3 stats
  - **触发脚本**：`python3 scripts/gen-{name}-cover.py /tmp/yj_{name}_cover.png`
- **回退触发判断**（什么情况直接 PIL 而不是再调 ljg-card 或 image_generate）：
  - 调研类封面（克制感 > 营销感）→ 直接 PIL
  - 视觉风格跟存量封面差距大（不是简单小修补）→ 直接 PIL
  - image_generate 多次重试仍 Latin 乱码 → 直接 PIL
- **实战救场**：5/5 调研封面（P17 Elementary / P18 D3 / P20 Temporal / P21 Prefect / P22 Pi）
- **文字零失真**（手写 font 字符串直接渲染）+ **生成快**（1 次 < 3 秒）+ **模板可复用**

## 模板

### 3 列撑满横版（16:9 博客封面，默认）

**核心改进**：容器高度减去上下边距后，所有列内部用 `space-between`，顶部/底部都有视觉元素，消除上下空白。

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<style>
  @import url('https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@400;500;700&display=swap');
  :root {
    --bg: #F0F3F7;
    --green: #8EAAB8;
    --pink: #C84A3B;
    --ink: #1f2024;
    --ink-light: #5C5350;
    --white: #FFFFFF;
    --serif: 'DM Serif Display', 'KingHwa_OldSong', Georgia, 'Noto Serif SC', serif;
    --sans: 'DM Sans', 'KingHwa_OldSong', -apple-system, 'PingFang SC', system-ui, sans-serif;
    --mono: 'SF Mono', 'Menlo', monospace;
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  html, body { width: 1920px; height: 1080px; background: var(--bg); }
  .page { width: 1920px; height: 1080px; background: var(--bg); position: relative; overflow: hidden; }
  .page > .grain { position: absolute; inset: 0; filter: url(#noise); opacity: 0.04; pointer-events: none; z-index: 100; }

  /* 关键：隐藏李继刚 colophon */
  .colophon { display: none !important; }

  .container {
    display: grid;
    grid-template-columns: 1.05fr 1.45fr 1.05fr;
    height: 984px;
    padding: 36px 44px;
  }

  /* Col 1: 品牌大字 */
  .col-left { display: flex; flex-direction: column; justify-content: space-between; align-items: flex-end; padding: 0 40px 0 34px; border-right: 1px solid rgba(45,41,38,0.10); text-align: right; }
  .brand { font: 400 210px/0.85 var(--serif); color: var(--ink); letter-spacing: -0.04em; line-height: 0.85; }
  .brand-dot { display: inline-block; width: 18px; height: 18px; background: var(--green); border-radius: 50%; margin-left: 8px; vertical-align: middle; position: relative; top: -90px; }
  .tagline { font: 500 24px/1.3 var(--sans); color: var(--ink); margin-top: 10px; letter-spacing: -0.01em; }
  .tagline em { color: var(--pink); font-style: normal; font-weight: 700; }

  /* Col 2: 核心卖点 */
  .col-mid { display: flex; flex-direction: column; justify-content: space-between; padding: 0 40px; border-right: 1px solid rgba(45,41,38,0.10); }
  .section-label { font: 500 13px/1 var(--mono); color: var(--green); letter-spacing: 0.22em; text-transform: uppercase; margin-bottom: 14px; }
  .headline { font: 400 48px/1.15 var(--serif); color: var(--ink); letter-spacing: -0.02em; margin-bottom: 18px; }
  .headline .accent { color: var(--pink); }
  .desc { font: 400 20px/1.5 var(--sans); color: var(--ink-light); margin-bottom: 24px; }

  .features { display: flex; flex-direction: column; gap: 10px; }
  .feature { display: flex; align-items: baseline; gap: 12px; font: 500 17px/1.3 var(--sans); color: var(--ink); }
  .feature .dot { width: 6px; height: 6px; background: var(--pink); border-radius: 50%; flex-shrink: 0; margin-top: 7px; }

  /* Col 3: 能力卡 + 交互方式 */
  .col-right { display: flex; flex-direction: column; justify-content: space-between; padding: 0 34px 0 38px; }
  .cap-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 9px; margin-bottom: 24px; }
  .cap-chip { padding: 13px 10px; background: var(--white); border: 1px solid rgba(45,41,38,0.10); text-align: center; font: 500 14px/1.2 var(--mono); color: var(--ink); letter-spacing: -0.01em; }
  .cap-chip.db { border-color: var(--green); color: #4f6d77; }
  .cap-chip.auth { border-color: var(--pink); color: #a13a2d; }
  .cap-chip.fn { border-color: var(--ink-light); color: var(--ink-light); }
  .cap-chip.ai { border-color: #3a6b5a; color: #2d5a47; }

  .modes { display: flex; flex-direction: column; gap: 9px; }
  .mode { display: flex; align-items: center; gap: 10px; font: 400 15px/1 var(--sans); color: var(--ink); }
  .mode .icon { width: 22px; height: 22px; border-radius: 4px; background: rgba(45,41,38,0.08); display: flex; align-items: center; justify-content: center; font: 500 11px/1 var(--mono); color: var(--ink-light); }
  .mode .name { font: 500 15px/1 var(--sans); color: var(--ink); }
  .mode .hint { font: 400 12px/1 var(--mono); color: var(--ink-light); margin-left: auto; }

  .footer { position: absolute; bottom: 0; left: 0; right: 0; height: 50px; background: var(--ink); display: flex; align-items: center; padding: 0 48px; }
  .footer-text { font: 400 14px/1 var(--sans); color: rgba(255,255,255,0.7); letter-spacing: 0.04em; }
  .footer-text .highlight { color: var(--green); font-weight: 500; }
</style>
</head>
<body>
<svg width="0" height="0" style="position:absolute">
  <filter id="noise">
    <feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="3" stitchTiles="stitch"/>
    <feColorMatrix type="saturate" values="0"/>
  </filter>
</svg>
<div class="page">
  <div class="grain"></div>
  <div class="container">
    <div class="col-left">
      <div class="big-number">7</div>
      <div class="big-label">件套</div>
      <div class="price-badge"><span class="dollar">$</span>60<span class="period">/月</span></div>
    </div>
    <div class="col-mid">
      <div class="comparison">
        <span class="from">100+</span><span class="arrow">→</span><span class="to">7</span>
      </div>
      <div class="subtitle">AI 工具箱 · 减法清单</div>
      <div class="kit-list">
        <div class="kit-item"><span class="num">01</span><span>OpenClaw + Claude Code</span><span class="price">$20</span></div>
        <div class="kit-item"><span class="num">02</span><span>Claude Pro</span><span class="price">$20</span></div>
        <div class="kit-item"><span class="num">03</span><span>即梦 / Midjourney</span><span class="price free">$0</span></div>
        <div class="kit-item"><span class="num">04</span><span>Perplexity Pro</span><span class="price">$20</span></div>
        <div class="kit-item"><span class="num">05</span><span>1Panel + Coolify</span><span class="price free">$0</span></div>
        <div class="kit-item"><span class="num">06</span><span>Claude (Notebook)</span><span class="price free">$0</span></div>
        <div class="kit-item"><span class="num">07</span><span>飞书 + AI 插件</span><span class="price free">$0</span></div>
      </div>
    </div>
    <div class="col-right">
      <div class="quote">
        <span class="lead">CORE</span>
        工具不是 <em>越多越好</em>，是 <em>越对越好</em>。
      </div>
    </div>
  </div>
</div>
</body>
</html>
```

## 升级路径

- **v0.1**：6 步 SOP + 1 个模板（3 列横版 16:9）
- **v0.2-1.0**：扩充模板库（ljg-card -l/b/baoyu-cover-image）
- **v1.1（2026-07-11 升级）**：发现 “中间空” 问题，弃用 3 列 space-between，推出「满版铺满」 4 种样式
- **v1.2**：新增“移动端可读性”硬规则（手机端 400px 下字 ≥ 14px / 装饰字 ≥ 11px / 删多余）
- **v1.3**：D · v4 final 固化为调研文章默认模板
- **v1.4（当前）**：新增 PIL 手画 fallback 路径（`scripts/gen-{name}-cover.py` 5 套模板），覆盖 image_generate minimax 文字失真 + 调研类克制调性场景

## 🎨 v1.3 标准模板：D · v4 final（深色科技背景）

**路径**：`templates/D_v4_final_tech_bg.html`

**用途**：所有调研类文章封面默认模板（100% 复用）

**结构**：
```
顶部：brand  + 顶部标签栏
左侧主区：预标 + 主标题（110px）+ 副标 + 命令块 + 3 个大数字
中央：12 点环形图（24 skills 分布）
右下：三能力卡（CORE / ENGINEERING / JUDGMENT）
底部：MIT + 开源 + STARS + 3 个平台 + URL
背景：节点连线 + 双辉光 + 3 重同心环
```

**适用内容**：AI 工具 / Coding Agent / 开发框架 / 技术品牌类文章

**什么时候不适用**：
- 哲学/认知/人生类 → 用 A · 沉思色调
- 人物故事 / 温暖类 → 用 B · 温暖色调
- 产品/营销/创意的 → 用 C · 明亮色调

**复用流程**：
1. 拷 `templates/D_v4_final_tech_bg.html` → `/tmp/yj_cover_{name}.html`
2. 替换品牌名 / 预标 / 主标题 / 副标 / 命令块 / 数字 / 技能点 / 3 卡内容
3. 背景元索保持原样
4. 渲染 → 自检 → 上传

**不允许**：
- 随便改主区布局（品牌一主区一环形一三卡 顺序不可换）
- 加新层信息（“3 层原则” 不可突破）
- 调色偏离（深色 + 细红 + 冷绿 三色体系）

## 与 ljg-card 关系

- **依赖**：ljg-card -i 模具（不重新发明轮子）
- **区别**：
  - ljg-card 是通用"铸"工具（李继刚美学）
  - yj-blog-cover 是飞熊博客专属封装（去除李继刚痕迹 + 飞熊调色）
- **不替代 ljg-card**：其他场景（PPT / 报告 / 海报）继续用 ljg-card

## 实战案例

### 2026-06-21 WP 156《AI 工具箱最佳实践》
- v1 (ljg-card -i 原版)：1080x1072（太高，比例不对）
- v2 (重新设计)：1080x1072（主题契合，比例仍不对）
- v3 (本 skill v0.1 模板)：1080x460 + 隐藏 colophon（准发布）
- 经验：先用 v3 SOP 跑出 v0，再根据反馈微调

### 2026-06-28 tmux / ghostty / taskmaster-ai 系列
- 统一改用 1920x1080 16:9
- capture.js 命令改为 `... 1920 1080`
- 已发布封面按新比例重做

### 后续 P2 系列（152/154/158）规划
- 152 技术人品牌：调色"温暖"或"商业"，主体球体辐射
- 154 Agent 平台：调色"技术"或"锐利"，点阵网络
- 158 微信生态：调色"锐利"（警示），闭环圆环

## 触发原则

**DO trigger**：
- 飞熊说"给 X 做封面"、"X 篇文章封面"
- 飞熊说"公众号头图"、"博客封面"
- 飞熊给 WP URL 让我做封面

**DON'T trigger**：
- 飞熊只要一段文字（不是图）
- 飞熊要表情包（用 meme-maker）
- 飞熊要 PPT（用 baoyu-slide-deck）

## Reference

- 父技能：`/home/east/.agents/skills/ljg-card/SKILL.md`
- 父技能 mode：`/home/east/.agents/skills/ljg-card/references/mode-infograph.md`
- 父技能品味：`/home/east/.agents/skills/ljg-card/references/taste.md`
- 飞熊博客：https://east196.cn
- 6 方法对比记录：`/home/east/.cache/covers/p2_156_methods/`
