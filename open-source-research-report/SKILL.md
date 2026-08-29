---
name: open-source-research-report
description: Produce a depth-first research report on an open-source project, ready to publish to WordPress. Handles same-name disambiguation, multi-source fetching, "kill your darlings" filtering (no source/internal info), ljg-card cover generation, and short-link delivery. Use when user says "调研 X", "X 怎么样", "X 是啥", "对比 X 和 Y", "研究下 X 开源库", or wants a public-facing article about a specific OSS project.
---

# open-source-research-report · 开源项目调研写作

从"调研一个开源项目"到"WordPress 短链接"，一条完整 pipeline。
**默认按公众读者视角（技术人 / INTP 友好）的深度向风格输出**。

---

## 措辞规则（调研报告大众化 · v2026-08-19 升级 · 飞熊硬要求）⭐

### 核心原则

- **调研报告是面向大众的，不是专门面向任何一个读者的**
- 个人品牌（飞熊）作为 IP 出现 OK，但**不能影响信息密度**
- 标题/内容面向大众搜索，专业词汇优先于个人品牌

### 标题规则

| 规则 | 说明 |
|---|---|
| **主词是大众搜索词** | "BI 栈" / "AI Agent 框架" / "数据治理" — 不是个人品牌 |
| **个人 IP 作前缀可选** | "飞熊出品 · 2026 BI 栈全景图" 或 不出现 |
| **不重复 IP** | "飞熊" 在标题里最多 1 次 |
| **SEO 关键词密度** | 标题 1-3 个核心关键词，不能堆砌 |

✅ 推荐格式：`[飞熊出品 · ] [大众搜索词]：[差异化卖点 1] + [卖点 2]，[场景]`
❌ 反例：`飞熊 BI 栈 2026 全景图 v2（飞熊出品）`（重复 IP）

### 正文措辞规则（替换清单）

| ❌ 禁止措辞 | ✅ 替代表达 |
|---|---|
| 飞熊做X / 飞熊跟X / 飞熊将X | 做X / 我们做X / 通常采用X |
| 飞熊客户 / 飞熊读者 | 客户 / 读者 / 企业客户 / 团队 |
| 飞熊实战 | 实战 |
| 飞熊已调研 / 飞熊觉得 | 已调研 / 推断 / 项目数据表明 |
| 飞熊跟你说 / 飞熊告诉你 | 客户问 / 常见问题 / 项目场景 |
| 飞熊实战选型经验 | 实战选型经验 |
| 飞熊真实落地能力 | 真实落地能力 |
| 飞熊 BI 栈系列 | **BI 栈系列**（单立品牌系名） |
| 飞熊的最高价值资产 | 技术品牌的最高价值资产 |
| 飞熊技术咨询变现路径 | 技术咨询变现路径 |
| 飞熊做技术咨询的终极形态 | 技术咨询的终极形态 |
| 飞熊的技术品牌 / 飞熊品牌 | 个人技术品牌 / IP 品牌 |
| 飞熊角度 / 飞熊实践 | 全景图角度 / 实践 |
| 飞熊对 X 的掌控 | 对 X 的掌控 |
| 飞熊将 / 飞熊告诉 | 推测 / 看来 / 项目表明 |
| 飞熊客户的反馈 | 客户反馈 |
| 飞熊整理了 | 整理了 / 梳理了 |
| 飞熊的 INSTAGRAM | 社交账号 / 联系方式 |

### "飞熊"出现频率上限

| 文章长度 | "飞熊"允许上限 |
|---|---|
| 1500 字以下 | ≤ 2 次（仅 byline） |
| 1500-5000 字 | ≤ 3 次（标题 + byline） |
| 5000+ 字 | ≤ 5 次（标题 + byline + 1 处正文重点） |
| 1MB+ 长文 | ≤ 8 次 |

### 作者署名规范

- ❌ `作者：飞熊 · 增长运营官 yunying 出品`
- ✅ `作者：yunying（增长运营官）` 或 `yunying（增长运营官）`
- ✅ 保留品牌符号: `by 飞熊 · yunying（增长运营官）`

### 内容风格规则

1. **第三人称视角**：避免"我跟你说"、"我跟你讲"、"我说"
2. **客观陈述**：用"该工具"、"该项目"、"本文"、"本系列"
3. **场景化**：用"客户场景"、"团队场景"代替"个人故事"
4. **数据驱动**：所有结论挂数据，不靠"我觉得"
5. **避免被动态**：不写"我跟X说"、"我会Z" — 用"建议"、"推荐"、"考虑"

### 实施优先级

- **P0**：本规则适用于所有调研报告发布
- **P1**：本 SKILL.md 由本节强制执行
- **P2**：发布前检查标题措辞 + 全文 grep “飞熊XXX” 出现次数

### 验证脚本

发布前检测脚本：

```bash
# 检查 “飞熊XXX” 私人口吻是否出现
grep -nE “飞熊(做|跟|将|客户|读|实战|觉得|说|告诉|的|角度|实践|对|整理|个人技术|技术咨询的|品牌)” article.md
# 期望：0 命中
```

## 适用场景

| 场景 | 走本 skill？ |
|---|---|
| 调研单个开源项目（"调研 dbx"）| ✅ |
| 同名项目消歧（GitHub 上同名/类似名多个）| ✅（自动）|
| 横向对比（"对比 DBeaver 和 DataGrip"）| ⚠️ 走本 skill 套对比模板 |
| 简单介绍（不要求深度）| ❌ 直接写 1 段 |
| 写技术教程 / how-to | ❌ 用 baoyu-format-markdown |

---

## Pipeline 总览

```
搜索候选 → 拉详情 → 消歧分类 → 选主角 → 出封面 → 写文章 → 过滤源信息 → 推 WP → 返短链
 Step 1      Step 2   Step 3       Step 4   Step 5   Step 6   Step 7           Step 8  Step 9
```

**当前状态**：wp-publish.py 已在发 WP 前自动做 md→html 转换（6/24 修 Bug），不依赖 WP 插件解析 Markdown。

---

## 调用参数

| 参数 | 必填 | 默认 | 说明 |
|---|---|---|---|
| `project_name` | ✅ | — | 项目名 / 关键词 |
| `angle` | ❌ | auto | auto / "对比 X" / "替代 Y" / "MCP 时代" |
| `output_path` | ❌ | workspace 根目录 | 产出 .md 路径 |
| `publish_to_wp` | ❌ | true | 是否推 WordPress |
| `category` | ❌ | 技术 | WP 分类：技术(4)/生活(3)/SEO(6) |

---

## Step 1: 搜索候选

用 `web_search` 搜，按顺序：

```
"<project_name>" GitHub
"<project_name>" 开源 库
"<project_name>" vs 替代
"<project_name>" license stars 2026
```

**输出**：候选项目列表（URL + 一句话定位 + stars + 最近 commit）。

---

## Step 2: 拉 GitHub 详情

对每个候选 `web_fetch` 拉 README + 官网，收集：

- 定位 / 一句话卖点
- stars / forks / license / commits
- 最近 commit 日期（判断活跃度）
- 主要功能列表

**输出**：每个候选 1 段摘要（< 200 字）。

---

## Step 3: 消歧 + 分类

| 类别 | 处置 |
|---|---|
| **A. 主角** | 重点深入讲（1 个，最多 2 个）|
| **B. 同名/相关** | 表格 1 行带过 + 链接 |
| **C. 不匹配 / 已死 / 跨领域** | **直接删掉，文章里不出现** |

> 飞熊读者看不到任何跟主题无关的项目。databricks 平台用户不需要看 dbx 数据库客户端——跨领域不匹配直接砍。

---

## Step 4: 主角决策算法

```
候选 N 个
  ↓ 活跃度筛选（commit < 30 天）
  ↓ 按 stars 排序
  ↓ 按"飞熊读者相关度"评分：
     +3  AI / LLM / MCP
     +3  数据 / 数据库
     +2  IoT / 硬件
     +2  工具 / 开发者效率
     +1  其他
  ↓ 取最高分当主角
  ↓ 并列第二 → 做双主角对比
  ↓ 其余全部删除
```

---

## Step 5: 封面

**优先走 yj-blog-cover**（内部用 ljg-card -i 信息图模具），而不是直接调 mmx-image / image_generate。

yj-blog-cover 内部用 ljg-card -i 信息图模具，默认 21:9 (1344x576) WP featured。

触发方式：
```
"给文章 [project_name] 做封面"  或  "博客封面 [project_name]"
```

fj-blog-cover 按 6 步 SOP 自动完成（确认 WP ID → 拉内容 → 选模具 → 渲染 → 自检 → 交付）。

**fallback 阶梯**（按顺序尝试）：

| 顺序 | 工具 | 触发条件 | 状态（2026-08-19 校准）|
|------|------|----------|---------------------|
| 1 | **yj-blog-cover** | available_skills 可见 | ❌ **不可用**（未上架，需手动触发 ljg-card -i）|
| 2 | **ljg-card `-i`** | 手动 Read SKILL.md + 调 `assets/capture.js` | ⚠️ 可用但需 node + playwright + 手动 HTML 拼接 |
| 3 | **mmx-image 模式 ② Cover** | 调 mmx-image skill | ⚠️ mmx-image 在列表但 **② Cover 子模式未提供独立 API** |
| 4 | **image_generate（minimax/image-01）** ✅ 实战首选 | 直接调 image_generate 工具 | ✅ **实战中实际使用**（image_generate 是 mmx-image 的底层）|
| 5 | **PIL 手画（scripts/gen-{name}-cover.py）** ✅ 实战兜底 | image_generate minimax/image-01 文字失真 / 乱码时 | ✅ **5/5 实战救场**（P17-P22：Elementary / D3 / Temporal / Prefect / Pi）|

**当前实战 = image_generate → PIL 手画兜底（#4 → #5）**：

- yj-blog-cover 未上架 → 跳到 #4 image_generate
- image_generate minimax/image-01 **文字常失真**（招牌乱码 / 数字乱码 / stats 全部乱码）→ 自动回退到 #5 PIL 手画
- 节省 ljg-card HTML 手工拼接 + playwright 部署成本
- 视觉风格跟存量 70+ 封面保持一致（1920×1080 + 深色科技风 + 六边形容器 + 3 锚点统计）
- vision model 分析：**更适合调研报告调性**（克制 / 杂志 / 白皮书感；ljg-card 太像产品落地页，会削弱专业可信度）
- PIL 手画优势：**文字零失真**（手写 font 字符串直接渲染）+ **生成快**（1 次 < 3 秒）+ **模板可复用**（改色 + 改 caps + 改 stats）

**为什么不去 #2 ljg-card**：

- 需 node + playwright 全套依赖
- 需手工拼 HTML 模板 + 调 capture.js 截图
- 耗时比 image_generate 长 3-5 倍
- 调研报告需求里"克制感" > "营销感"，ljg-card 反而是负向

**image_generate prompt 模板**（标准化复用）：

```
Modern dark tech cover image for [项目名] article. Center title "[项目名]", subtitle "[一句话卖点]". Show N large hexagonal containers representing [关键能力 1/2/3]. Add a glowing orange connection line going up to a stylized [顶部] hexagon. Background deep navy (#0a0e27) with subtle grid pattern. Add 3 anchor stats: "[stat 1]", "[stat 2]", "[stat 3]". Modern flat-design tech aesthetic, professional engineering blog cover, 1920x1080, no people.
```

**fallback 后盖上游**：yj-blog-cover 后续若上架，可在 Step 5 顶部优先走；若 yj-blog-cover 输出跟存量 70+ 风格冲突，先发一篇文章对比，再决定是否批量迁移存量。

**PIL 手画实战经验**（2026-08-29 沉淀，5 套模板 5/5 一次成功）：

- **位置**：`scripts/gen-{name}-cover.py`，固定 1920×1080 + RGB，输出 `~/.cache/covers/yj_{name}_*.png`
- **模板复用链**：gen-elementary-cover.py → gen-d3-cover.py → gen-temporal-cover.py → gen-prefect-cover.py → gen-pi-cover.py（5 套一脉相承，只改色 + 改 caps + 改 stats）
- **改 3 参数复用**（不需要重写）：
  1. **主色 + 顶部 hex 色**（项目 brand，如 dbt 橙 #FF694A / D3 蓝 #137BCD / Temporal teal #00939B / Prefect forest #08504C / Pi indigo #5A4FCF）
  2. **6 个 hex 容器 caps**（核心能力关键词，2 行×3 列）
  3. **底部 3 anchor stats**（stars / license / 最新版日期）
- **固定结构**（不动）：顶部项目 hex + 主标题 140pt + 副标题 + 6 hex 容器 2 行×3 列 + 底部 3 stats
- **触发脚本**：
  ```bash
  python3 scripts/gen-{name}-cover.py /tmp/yj_{name}_cover.png
  ```
- **回退触发判断**（什么情况直接 PIL 而不是再调 image_generate）：
  - 返回图含明显 Latin 乱码招牌（如 "POCISELIY EAYANOSIDLE"）→ 直接 PIL
  - 3 个 hex 容器名任一看不清 → 直接 PIL
  - 标题 / 副标题 / stats 中任一元素失真 → 直接 PIL
  - 视觉风格跟存量封面差距大（不是简单小修补）→ 直接 PIL

---

## Step 6: 写文章（深度向 9 段模板）

### 标题（2 类）

| 类型 | 公式 |
|---|---|
| **工具/库类**（有竞品）| `[项目] 调研：[卖点 1] + [卖点 2]，[场景] vs [对比工具] 香在哪里` |
| **方法/概念类**（无竞品）| `[项目]调研：[核心卖点]，[量化钩子或一句话价值]` |

### 正文结构

```
# [标题]
> 1 句话钩子（项目定位 + 关键能力）

## 写在前面（破题：为什么混乱 / 为什么值得看）
## 一、它解决什么问题
  → 1 句话卖点 + 基本信息表格
## 二、[核心维度 1：支持范围 / 能力 / 适用场景]
  → 列表 + 子分类
## 三、[核心维度 2：差异化能力（AI / MCP / 新协议）]
  → 编号列表，每个能力 2-3 句
## 四、[编辑器 / 浏览 / 工具能力]
  → 表格（4-6 行）
## 五、对比 [主流工具 A/B/C/D]
  → 对比表 + 1 段结论
## 六、实战：3 步跑通
  → 步骤 + 代码块
## 七、风险与坑
  → 3-4 条
## 八、总结
  → 3 个"最值得装的理由" + 1 句"先试一周"
## 参考
  → 5-8 条链接
```

> 方法类（框架/理念/工作流）优先放**数字/机制名**，不硬套对比模板。

---

## Step 7: 过滤源信息

**默认删掉**的内容：

| 类型 | 示例 |
|---|---|
| 调研时间戳 | `> 调研时间：2026-06-14` |
| 调研者立场 | `> 立场：作为 INTP 技术人` |
| 人设标签 | `INTP 视角` / `飞熊（INTP 技术人）` |
| 内部指令 | `对飞熊：先装...` |
| 跨领域不匹配 | 不相关同名项目 |
| 死项目八卦 | 已死项目历史 |
| 内部门号 footer | `*让技术被看见...*` |

**保留**：事实（stars/license/commits）、外部链接、实战代码/配置。

---

## Step 8: 推 WordPress

```bash
python3 ~/.openclaw/workspace-yunying/scripts/wp-publish.py <article.md> 技术 [--cover <封面.png>]
```

⚠️ **同名 slug 自动更新已有文章**（不会重复创建）。

wp-publish.py 内部流程：
1. 读 md → strip frontmatter + 标题行
2. **Markdown → HTML 转换**（python-markdown + tables/fenced_code/nl2br 扩展，绕过 WP 插件 Bug）
3. 封面图 magic bytes 验证 → 上传 media → 设 featured_media
4. POST 到 WP REST API（`/wp-json/wp/v2/posts`）
5. 输出 `WP_POST_ID=<id>` + `WP_FEATURED_MEDIA=<id>` 供下游抓取

### 常见问题

| 问题 | 处理 |
|---|---|
| slug 重复 → 更新旧文 | 正常行为，不需要处理 |
| 改了标题 → slug 变了 → 重复创建 | 先 DELETE 旧文章，再发新文章 |
| featured_media=0 | 封面 upload 失败，检查 magic bytes |
| WP 渲染后 `##` 标题丢进 `<p>` | **已修复**：md→html 在发送前完成，不依赖 WP 插件 |
| 文章被截断 | wp-publish.py 6/24 已修，发送前做完整 md→html 转换 |

---

## Step 9: 返回短链接 ⭐ Markdown 格式

格式：`https://east196.cn/?p={WP_POST_ID}`（注意：`?p={id}` 会 301 跳到完整 URL，标题匹配）

**飞熊硬要求（2026-08-17 升级）**：最终输出**必须包含带 ID 的 WP 链接，Markdown 格式，可以直接点开看**。

### 最终输出模板（强制）

每篇调研报告末尾**必须包含**以下 Markdown 链接块：

```markdown
## 📎 WordPress 链接

- **官方链接**：[《{文章标题}》](https://east196.cn/?p={WP_POST_ID})
- **短链**：`https://east196.cn/?p={WP_POST_ID}`
- **WordPress API ID**：{WP_POST_ID}
- **状态**：published · {发布日期}
```

### 链接格式规范

| 形式 | 模板 | 使用场景 |
|------|------|----------|
| **官方链接**（锚文本 Markdown）| `[《重点项目调研》](https://east196.cn/?p=326)` | 主要推荐 |
| **短链**（纯文本）| `https://east196.cn/?p=326` | 复制粘贴 |
| **完整 slug 链接** | `https://east196.cn/{year}/{month}/{day}/{slug}` | SEO 友好（less useful） |

**最佳实践**：
- ✅ 主链接用 Markdown 形式 `[文字](URL)` — 飞熊手机/桌面端均可直接点击
- ✅ 同时提供纯 URL 形式 — 方便复制
- ✅ 报告里提到的"短链"必须用 `?p={id}` 格式（不是完整 slug）— 短且稳定
- ❌ 不要只放纯 URL 不带 Markdown 文本 — 飞熊看不出标题
- ❌ 不要省略 WP_POST_ID — 飞熊要"带 ID 的链接"

### 报告中的引用位置

每次报告**必须出现 Markdown 链接**的位置：

1. **标题 / 摘要之后**——首次提到文章时
2. **"## ✅ 已发布" 表格**——每条带链接的验证项加 `(https://east196.cn/?p=326)` 锚文本
3. **"## 📝 报告结构"**——如有"另见 / 参考"链接
4. **"## 📋 进度 / 推进表"**——已发布的项目加链接
5. **"## 💡 数据小观察"**或文末**"## 📎 WordPress 链接"**——独立链接块

### 完整示例

```markdown
## ✅ DSH（TOP 5 · #1）已完成

| 验证项 | 证据 |
|------|------|
| **WP 发布** | [《DeepSeek Harness (DSH) 调研》在 east196.cn](https://east196.cn/?p=340) |
| **WordPress API ID** | 340 |
| **短链 200** | `curl -L https://east196.cn/?p=340` → STATUS=200 · 354ms |

## 📎 WordPress 链接

- **官方链接**：[《DeepSeek Harness (DSH) 调研：3 天 13 万 stars 的"一切皆插件"Agent 框架》](https://east196.cn/?p=340)
- **短链**：`https://east196.cn/?p=340`
- **WordPress API ID**：340
- **状态**：published · 2026-08-17
```

---

## 与相关 skill 的关系

| skill | 关系 |
|---|---|
| **yj-blog-cover** | 封面生成（优先，自动调 ljg-card -i）|
| mmx-image | fallback（yj-blog-cover 不可用时）|
| scripts/gen-{name}-cover.py | **PIL 手画兜底**（image_generate minimax 文字失真时 5/5 救场）|
| wp-publish.py | **必调**，推 WordPress |</newText>
| content-pipeline.sh | 可选，做总入口（cron / pipeline）|

---

## 失败回退

| 失败 | 回退 |
|---|---|
| web_fetch 失败 | 用 web_search 摘要代替 |
| yj-blog-cover 不可用 | 回退 mmx-image 模式 ② Cover |
| mmx-image 也失败 | 跳过封面，文章照发 |
| image_generate minimax/image-01 文字失真 / 乱码 | **PIL 手画救场**（参考 scripts/gen-*-cover.py 5 套模板，改 3 参数复用）|
| wp-publish 失败 | 保留 .md 在 workspace，手工检查后重试 |</newText>
| 主角决策不出 | 问飞熊："X 项目你想重点讲哪个？" |
| WP 渲染异常（表格/标题丢失）| **已修**：wp-publish.py 6/24 前做 md→html 转换 |

---

## 已知坑（实踩过的）

| 日期 | 问题 | 修复 |
|---|---|---|
| 6/19 | featured_media 翻车 3 次（wp-publish.py 不处理 featured_media）| wp-publish.py 加 `--cover` 自动绑定 |
| 6/24 | `##` 标题被 Markup Markdown 插件丢进 `<p>`，文章后半截截断 | wp-publish.py 加 md→html 转换（stepfun/step-3.5-flash 触发），不依赖 WP 插件解析 |
| 8/28-29 | image_generate minimax/image-01 文字失真通病（5/5 失败）→ 5 篇调研全靠 PIL 手画救场（P17 Elementary / P18 D3 / P20 Temporal / P21 Prefect / P22 Pi）| 沉淀 PIL fallback 进 Step 5 #5 + 失败回退表 + scripts/gen-*-cover.py 5 套模板复用链 |
