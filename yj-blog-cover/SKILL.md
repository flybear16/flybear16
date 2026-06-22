---
name: "yj-blog-cover"
description: "飞熊博客封面：WP 21:9 + 公众号 2.35:1。集成 ljg-card -i，隐藏 colophon，技术色调，6 步 SOP。"
---

# yj-blog-cover: 飞熊博客封面生成器

## 目标

专门为飞熊博客（east196.cn WordPress）和微信公众号生成封面图。集成 ljg-card -i 信息图模具最佳实践，沉淀 2026-06-21 这轮 6 方法对比 + 3 次重新设计累积的经验。

解决三个核心痛点：
1. ljg-card colophon 硬编码"李继刚"（飞熊博客不能用）
2. ljg-card 固定 1080xauto（不是公众号头图 2.35:1 / WP featured 21:9 比例）
3. ljg-card 缺"博客场景"模板（每次都要从零写 HTML）

## 触发词

- "博客封面"、"做封面"、"给文章 X 做封面"、"east196.cn 封面"
- "公众号头图"、"公众号封面"、"微信头图"
- "X 篇文章封面"、"cover for post X"
- "yj cover"、"yj blog cover"

## 默认参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| 模具 | ljg-card -i | 信息图模式（最适合博客封面） |
| 比例 | 2.35:1 (1080x460) | 公众号头图标准 |
| 备选比例 | 21:9 (1344x576) | WP featured_media |
| 调色 | 技术 (--bg #F0F3F7, --green #8EAAB8, --pink #C84A3B) | 工程师感硬核极简 |
| 字体 | DM Serif Display + DM Sans + KingHwa_OldSong | 避 Inter |
| colophon | 隐藏 | 飞熊博客不要"李继刚" |
| 弹点 | ≤ 2 处 | 90/8/2 法则 |
| 字号张力 | ≥ 10:1 | 最大 / 最小元素比例 |

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
  - 固定 `width: 1080px; height: 460px`（公众号头图）
  - 或固定 `width: 1344px; height: 576px`（WP featured）
- 调 capture.js：
  - 公众号：`node /home/east/.agents/skills/ljg-card/assets/capture.js /tmp/yj_cover_{name}.html ~/.cache/covers/yj_{name}.png 1080 460`
  - WP featured：`... 1344 576`
- **非 fullpage 模式**（不传第 5 个参数）

### 5. 自检清单
- [ ] colophon 已隐藏？（grep "李继刚" 应该是空）
- [ ] 弹点色 ≤ 2 处？
- [ ] 最大元素 ≥ 10:1 张力？
- [ ] 比例匹配目标（2.35:1 / 21:9）？
- [ ] 移动端 350-400px 缩放下文字可读？（关键字段字号 ≥ 18px）
- [ ] 调色与内容主题匹配？
- [ ] 没用 Inter 字体？
- [ ] 没用纯黑 #000？（用 #1f2024 / #2D2926 替代）
- [ ] 没用 AI 紫蓝渐变？

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
- **症状**：capture.js fullpage 模式输出 1080xauto（高度由内容决定），不是 2.35:1
- **根因**：capture.js fullpage 自动按 body.scrollHeight 截
- **修复**：
  - HTML body 设固定 `width: 1080px; height: 460px`
  - capture.js 用非 fullpage 模式（不传第 5 个参数）
- **预防**：模板默认提供 2 套固定尺寸（1080x460 公众号 / 1344x576 WP）

### 坑 3：minimax AI Latin 乱字
- **症状**：baoyu-cover-image / mmx-image 生成的图常带"乱字招牌"（如"KOHCRASTO"）
- **根因**：minimax/image-01 渲染 Latin 文字不可控
- **解决**：**优先用 ljg-card**（HTML 模板渲染，文字可控）

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

## 模板

### 3 列横版（2.35:1 公众号头图，默认）

完整 HTML 模板（参考 v3 设计，已验证可发布）：

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
  html, body { width: 1080px; height: 460px; background: var(--bg); }
  .page { width: 1080px; height: 460px; background: var(--bg); position: relative; overflow: hidden; }
  .page > .grain { position: absolute; inset: 0; filter: url(#noise); opacity: 0.04; pointer-events: none; z-index: 100; }

  /* 关键：隐藏李继刚 colophon */
  .colophon { display: none !important; }

  .container {
    display: grid;
    grid-template-columns: 0.85fr 1.4fr 0.75fr;
    height: 460px;
    padding: 60px 0 50px;
  }

  /* Col 1: 大数字 + badge */
  .col-left { display: flex; flex-direction: column; justify-content: center; align-items: flex-end; padding: 0 30px 0 28px; border-right: 1px solid rgba(45,41,38,0.10); text-align: right; }
  .big-number { font: 400 220px/0.82 var(--serif); color: var(--ink); letter-spacing: -0.05em; line-height: 0.82; }
  .big-label { font: 500 30px/1 var(--sans); color: var(--ink); margin-top: 4px; }
  .price-badge { display: inline-flex; align-items: baseline; gap: 1px; margin-top: 22px; padding: 9px 16px 7px; background: var(--pink); color: var(--white); font: 500 30px/1 var(--serif); }
  .price-badge .dollar { font: 400 19px/1 var(--serif); }
  .price-badge .period { font: 400 16px/1 var(--sans); margin-left: 4px; opacity: 0.92; }

  /* Col 2: 100+ → 7 + 7 件套列表 */
  .col-mid { display: flex; flex-direction: column; justify-content: center; padding: 0 30px; border-right: 1px solid rgba(45,41,38,0.10); }
  .comparison { display: flex; align-items: baseline; gap: 10px; font: 400 42px/1 var(--serif); color: var(--ink); letter-spacing: -0.02em; }
  .comparison .from { color: var(--ink-light); text-decoration: line-through; text-decoration-thickness: 2.5px; text-decoration-color: var(--green); }
  .comparison .arrow { font: 400 32px/1 var(--sans); color: var(--green); }
  .comparison .to { color: var(--pink); }
  .subtitle { font: 500 18px/1.2 var(--sans); color: var(--ink); margin-top: 8px; }
  .kit-list { display: flex; flex-direction: column; margin-top: 16px; }
  .kit-item { display: grid; grid-template-columns: 26px 1fr auto; align-items: baseline; gap: 10px; padding: 5px 0; font: 500 15px/1.2 var(--sans); color: var(--ink); border-top: 1px solid rgba(45,41,38,0.10); }
  .kit-item:last-child { border-bottom: 1px solid rgba(45,41,38,0.10); }
  .kit-item .num { font: 400 12px/1 var(--mono); color: var(--green); }
  .kit-item .price { font: 400 13px/1 var(--mono); color: var(--ink-light); }
  .kit-item .price.free { color: var(--green); }

  /* Col 3: 金句 */
  .col-right { display: flex; align-items: center; justify-content: center; padding: 0 24px 0 28px; }
  .quote { font: 400 22px/1.45 var(--serif); color: var(--ink); font-style: italic; letter-spacing: -0.005em; }
  .quote em { color: var(--pink); font-style: normal; font-weight: 500; display: block; margin-top: 8px; }
  .quote .lead { display: block; color: var(--green); font: 500 13px/1 var(--sans); font-style: normal; letter-spacing: 0.15em; text-transform: uppercase; margin-bottom: 8px; }
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

- **v0.1（当前）**：6 步 SOP + 1 个模板（3 列横版 2.35:1）
- **v0.2**：加 ljg-card -l 长图模板（正文头图 1080xauto）
- **v0.3**：加 ljg-card -b 大字模板（小红书附件图 1080x1440）
- **v0.4**：集成 baoyu-cover-image 5 维精调（4K 渲染备选）
- **v0.5**：自动 WP upload + 替换 featured_media（用 wp-publish.py --cover）
- **v1.0**：批量模式（一次给多篇 WP ID，输出 N 张封面）

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
