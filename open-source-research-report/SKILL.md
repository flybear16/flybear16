---
name: open-source-research-report
description: Produce a depth-first research report on an open-source project, ready to publish to WordPress. Handles same-name disambiguation, multi-source fetching, "kill your darlings" filtering (no source/internal info), mmx-image cover generation, and short-link delivery. Use when user says "调研 X", "X 怎么样", "X 是啥", "对比 X 和 Y", "研究下 X 开源库", or wants a public-facing article about a specific OSS project.
---

# open-source-research-report · 开源项目调研写作

从"调研一个开源项目"到"WordPress 短链接"，9 步完整 pipeline，**默认按飞熊读者群（技术人 / INTP 友好）的深度向风格输出**。

## 适用场景

| 场景 | 是否走本 skill |
|---|---|
| 调研单个开源项目（"调研 dbx"）| ✅ |
| 同名项目消歧（GitHub 上同名 / 类似名多个）| ✅（自动）|
| 横向对比（"对比 DBeaver 和 DataGrip"）| ⚠️ 走本 skill 套对比模板 |
| 简单介绍文章（不要求深度）| ❌ 直接写 1 段 |
| 写技术教程 / how-to | ❌ 用 baoyu-format-markdown |

## 输入参数

| 参数 | 必填 | 默认 | 说明 |
|---|---|---|---|
| `project_name` | ✅ | — | 项目名 / 关键词 |
| `angle` | ❌ | auto | auto / "对比 X" / "替代 Y" / "MCP / AI 时代" |
| `output_path` | ❌ | workspace 根目录 | 产出 .md 路径 |
| `publish_to_wp` | ❌ | true | 是否推 WordPress |
| `category` | ❌ | 技术 | WP 分类 |

## 9 步工作流

### Step 1: Web 搜索同名/主流项目

用 `web_search` 搜：
- `"<project_name>" GitHub`
- `"<project_name>" 开源 库`
- `"<project_name>" vs 替代`

**输出**：候选项目列表（带 URL + 简介）

### Step 2: Web fetch 每个项目的 GitHub 详情

对每个候选项目 `web_fetch` 拉：
- README（项目自述）
- 主页 / 官网
- 最近 commit 日期（活跃度）
- stars / license

**输出**：每个候选的 1 段摘要（< 200 字）

### Step 3: 去重 + 分类

按"是否跟飞熊读者相关"分 3 类：

| 类别 | 处置 |
|---|---|
| **A. 主角** | 重点深入讲（1 个，最多 2 个）|
| **B. 同名/相关** | 表格 1 行带过 + 链接 |
| **C. 不匹配 / 已死 / 跨领域** | **删掉**，文章里不出现 |

**原则**：**飞熊读者看不到任何跟主题无关的项目**。databricks 平台用户不需要看 dbx 数据库客户端——这种跨领域不匹配直接砍。

### Step 4: 决定主角

优先级：
1. 活跃度（最近 commit < 30 天）
2. stars 数量
3. 跟飞熊读者群（AI / 数据 / 工具 / IoT）的相关度
4. 是否有独特亮点（MCP / AI / 新协议）

**自动选 1 个主角**。如果 2 个都活跃且相关，做成"双主角对比"模式。

### Step 5: 封面（调 yj-blog-cover skill）

**优先走 yj-blog-cover（博客封面专属 skill）**，而不是直接调 mmx-image。

yj-blog-cover 内部用 ljg-card -i 信息图模具，已解决 3 个已知坑：
- ✅ colophon 不显示"李继刚"
- ✅ 默认 2.35:1 (1080x460) 公众号头图 / 21:9 (1344x576) WP featured
- ✅ HTML 模板渲染，文字可控（无 minimax Latin 乱字）

**调用方式**：
```
触发词："给文章 [project_name] 做封面" 或 "博客封面 [project_name]"
```

yj-blog-cover 按 6 步 SOP 自动完成：
1. 确认文章 WP ID / URL
2. 拉文章内容（标题 + 副标题 + 核心卖点）
3. 选模具（默认 ljg-card -i）
4. 渲染（capture.js → PNG）
5. 自检（colophon 隐藏 / 弹点 ≤ 2 / 比例正确）
6. 交付（保存 + dry-run / upload 二选一）

**调研报告默认配置**：
- 比例：21:9 (1344x576) — WP featured_media 标准
- 调色：技术（--bg #F0F3F7, --green #8EAAB8, --pink #C84A3B）
- 比例备选：2.35:1 (1080x460) — 公众号头图

**fallback**：如果 yj-blog-cover 不可用，回退到 mmx-image 模式 ② Cover。

### Step 6: 写文章

**套下面"飞熊深度向结构"模板**——这是 2026-06-14 dbx 131 那版验证过的结构。

#### 飞熊深度向结构（9 段）

```
# [项目] 调研：[核心卖点 1] + [核心卖点 2] + [核心卖点 3]，[场景] [对比工具] 香在哪里
> 1 句话钩子（项目定位 + 关键能力）

## 写在前面（破题：为什么混乱 / 为什么值得看）
- 1 段说明"为什么这件事值得花时间看"

## 一、它解决什么问题
- 1 句话卖点 + 体积 / 平台 / 协议 / GitHub 表格

## 二、[核心维度 1：支持范围 / 能力 / 适用场景]
- 列表 + 子分类（30+ 那种规模）

## 三、[核心维度 2：差异化能力（AI / MCP / 新协议）]
- 编号列表，每个能力 2-3 句

## 四、编辑器 / 浏览 / 工具能力
- 表格（4-6 行）

## 五、对比 [主流工具 A/B/C/D]
- 6-7 列对比表（体积、跨平台、价格、AI、MCP、多数据库、商用风险）
- 1 段结论

## 六、实战：3 步跑通
- 步骤 1 / 2 / 3 + 代码块 / 配置文件

## 七、决策表（按场景推荐）
- 5-6 行（场景 → 选谁 → 理由）

## 八、风险与坑
- 3-4 条（AGPL / 安全检查 / 协议演化 / 社区语言）

## 九、总结
- 3 个"最值得装的理由" + 1 句"先试一周"

## 参考
- 5-8 条（GitHub 官方源 + 中文社区源）
```

### Step 7: 过滤源信息（**关键**）

**默认删**这些内容（飞熊读者不需要知道）：

| 类型 | 示例 | 处置 |
|---|---|---|
| 调研时间戳 | `> 调研时间：2026-06-14` | ❌ 删 |
| 调研者立场 | `> 立场：作为 INTP 技术人` | ❌ 删 |
| 调研者人设 | `INTP 视角` / `飞熊（INTP 技术人）` | ❌ 改成"实用主义" / "技术人" |
| 调研对象画像 | `对飞熊：先装...` | ❌ 改成"建议你" |
| 不相关同名项目 | databricks 那种跨领域不匹配 | ❌ 整段删 |
| 已死项目八卦 | iarna/DBIx 公链 | ❌ 删 |
| 内部脚手架 | `*让技术被看见...*` footer | ⚠️ 看情况（这是飞熊 tagline） |

**保留**：
- ✅ 事实（stars、license、commit 数）
- ✅ 外部参考链接（GitHub、官网、迁移文档）
- ✅ 实战代码 / 配置

### Step 8: 推 WordPress

**直接调 `wp-publish.py`**：

```
python3 ~/.openclaw/workspace-yunying/scripts/wp-publish.py <article.md> 技术
```

⚠️ **同名 slug 自动更新已有文章**（避免重复）。如果改了标题导致 slug 变了：
- 旧文章**手工 DELETE**（用 `curl -X DELETE .../posts/<id>?force=true`）
- 保留新文章
- 给新文章**设 featured_media**（POST 媒体 + featured_media endpoint）

### Step 9: 返回短链接

按 `https://east196.cn/?p={id}` 格式返回。

## 主角决策算法（agent 必读）

```
候选项目 N 个
  ↓
活跃度筛选（最近 commit < 30 天）
  ↓
按 stars 排序
  ↓
按"飞熊读者相关度"评分：
  + 3  AI / LLM / MCP
  + 3  数据 / 数据库
  + 2  IoT / 硬件
  + 2  工具 / 开发者效率
  + 1  其他
  ↓
取最高分当主角
  ↓
如有并列第二，做对比
  ↓
其余全部不出现
```

## mmx-image 调用模板（agent 必读）

5 维度 prompt 生成规则（按 mmx-image SKILL.md 模式 ②）：

```
type=<conceptual|hero|...>, palette=<cool|warm|...>, rendering=<flat-vector|...>, 
mood=<balanced|...>, text=none, font=clean

→ "<type> illustration, <palette> color palette, <rendering> rendering, 
<mood> contrast, <主元素>, [1-2 句具体描述], no text, 16:9 widescreen"
```

飞熊默认 blueprint preset：
```
"conceptual illustration, cool color palette (deep blue and cyan), 
digital rendering with precise geometric lines and grid patterns, 
balanced contrast, technical schematic aesthetic, 
[主元素], modern minimal composition, no text, 16:9 widescreen"
```

## 与其他 skill 的关系

| skill | 关系 |
|---|---|
| **yj-blog-cover** | **封面生成**（优先走这个，自动调 ljg-card -i）|
| mmx-image | fallback（yj-blog-cover 不可用时）|
| baoyu-format-markdown | 可选，typography 微调 |
| baoyu-article-illustrator | 不用（yj-blog-cover 已覆盖封面）|
| baoyu-cover-image | 不用（yj-blog-cover 已覆盖）|
| baoyu-xhs-images | 不用（调研报告走 WordPress，不走 XHS）|
| baoyu-post-to-wechat | 不用（IP 白名单未解，公众号不调）|
| wp-publish.py | **必调**，推 WP |
| content-pipeline.sh | **可选**，做总入口 |

## 不在本 skill 范围

- ❌ 写技术教程 / how-to（用 baoyu-format-markdown + baoyu-image-gen）
- ❌ 写产品评测（"iPhone 17 怎么样"那种非开源项目）
- ❌ 写中文社区分析（"Vibe Coding 是什么"）
- ❌ 写新闻稿 / 时事评论

## 失败回退

| 失败 | 回退 |
|---|---|
| web_fetch 失败 | 用 web_search 摘要代替 |
| minimax 出图失败 | 1 次重试 → 换 openai/gpt-image-1.5 → 还失败走 yj-blog-cover → 全失败就跳过封面，文章照发 |
| wp-publish 失败 | 保留 .md 在 workspace，手工检查后重试 |
| 主角决策不出 | 问飞熊（"X 项目你想重点讲哪个？"）|

## 备注

- 飞熊读者群：**AI / IoT / 工具 / 数据**领域的技术人（INTP 偏多）
- 风格：深度向，不口水；对比表 1 段结论；不写废话引子
- **标题生成（2 类）**：
  - **工具/库类**（有横向竞品）：`[项目] 调研：[卖点 1] + [卖点 2]，[场景] vs [对比工具] 香在哪里`
  - **方法/概念类**（框架/理念/工作流，无竞品）：`[项目]调研：[核心卖点]，[量化钩子或一句话价值]`
- 方法类优先放数字 / 机制名（如"YAGNI 阶梯 + 54% 压缩率"），不硬套对比模板
- 单图生成时间 10-30 秒，**单调研报告通常 < 5 分钟出**
