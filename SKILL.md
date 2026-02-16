---
name: xhs-note-creator
description: 小红书笔记素材创作技能。当用户需要创建小红书笔记素材时使用这个技能。技能包含：根据用户的需求和提供的资料，撰写小红书笔记内容（标题+正文），生成图片卡片（封面+正文卡片），以及发布小红书笔记。
---

# 小红书笔记创作技能

这个技能用于创建专业的小红书笔记素材，包括内容撰写、图片卡片生成和笔记发布。

## 使用场景

- 用户需要创建小红书笔记时
- 用户提供资料需要转化为小红书风格内容时
- 用户需要生成精美的图片卡片用于发布时

## 环境配置

本技能的 Python 脚本依赖项已安装在 `.venv` 虚拟环境中。

### Python 脚本执行方式

**必须使用 venv 中的 Python 解释器**：

```bash
# 使用项目目录下的 .venv
.venv/bin/python scripts/xxx.py

# 或者激活 venv 后执行
source .venv/bin/activate
python scripts/xxx.py
```

### 依赖管理

如需安装新依赖：

```bash
.venv/bin/pip install <package>
```

## 工作流程

### 第一步：撰写小红书笔记内容

根据用户需求和提供的资料，创作符合小红书风格的内容：

#### 标题要求
- 不超过 20 字
- 吸引眼球，制造好奇心
- 可使用数字、疑问句、感叹号增强吸引力
- 示例：「5个让效率翻倍的神器推荐！」「震惊！原来这样做才对」

#### 正文要求
- 使用良好的排版，段落清晰
- 点缀少量 Emoji 增加可读性（每段 1-2 个即可）
- 使用简短的句子和段落
- 结尾加上：关注我，第一时间获取相关资讯
- 结尾给出 SEO 友好的 Tags 标签（5-10 个相关标签）

### 第二步：生成 Markdown 文档

**注意：这里生成的 Markdown 文档是用于渲染卡片的，必须专门生成，禁止直接使用上一步的笔记正文内容。**

#### 输出目录规则

每篇笔记必须创建独立的子文件夹，避免覆盖之前的笔记：

```
output/<slug>/          # 例如 output/openclaw-openai/
├── content.md          # Markdown 源文件
├── cover.png           # 封面
├── card_1.png          # 正文卡片
├── card_2.png
└── ...
```

- `<slug>` 为笔记主题的英文短标识（小写，用 `-` 连接），例如 `openclaw-openai`、`5-productivity-tools`
- Markdown 文件统一命名为 `content.md`，保存在该子文件夹内
- 渲染输出目录 `-o` 指向该子文件夹

Markdown 文件应包含：

1. YAML 头部元数据（封面信息）：
```yaml
---
emoji: "🚀"           # 封面装饰 Emoji
title: "大标题"        # 封面大标题（不超过15字）
subtitle: "副标题文案"  # 封面副标题（不超过15字）
---
```

2. 用于渲染卡片的 Markdown 文本内容：
   - 当待渲染内容必须严格切分为独立的数张图片时，可使用 `---` 分割线主动将正文分隔为多个卡片段落（每个段落文本控制在 200 字左右），输出图片时使用参数`-m separator`
   - 当待渲染内容无需严格分割，生成正常 Markdown 文本即可，跟下方分页模式参数规则按需选择

完整 Markdown 文档内容示例：

```markdown
---
emoji: "💡"
title: "5个效率神器让工作效率翻倍"
subtitle: "对着抄作业就好了，一起变高效"
---

# 📝 神器一：Notion

> 全能型笔记工具，支持数据库、看板、日历等多种视图...

## 特色功能

- 特色一
- 特色二

# ⚡ 神器二：Raycast

\`\`\`
可使用代码块来增加渲染后图片的视觉丰富度
\`\`\`

## 推荐原因

- 原因一
- 原因二
- ……


# 🌈 神器三：Arc

全新理念的浏览器，侧边栏标签管理...

...

```

### 第三步：渲染图片卡片

将 Markdown 文档渲染为图片卡片。使用以下脚本渲染：

```bash
# 使用 venv（Python 渲染需要依赖）
.venv/bin/python scripts/render_xhs.py <markdown_file> [options]

# 或使用 Node.js（无需 venv，推荐）
node scripts/render_xhs.js <markdown_file> [options]
```

- 输出目录为笔记的独立子文件夹（如 `output/openclaw-openai/`）
- 生成的图片包括：封面（cover.png）和正文卡片（card_1.png, card_2.png, ...）

#### 渲染参数

| 参数 | 简写 | 说明 | 默认值 |
|---|---|---|---|
| `--output-dir` | `-o` | 输出目录 | 当前工作目录 |
| `--theme` | `-t` | 排版主题 | `playful-geometric` |
| `--mode` | `-m` | 分页模式 | `separator` |
| `--width` | `-w` | 图片宽度 | `1080` |
| `--height` |  | 图片高度（`dynamic` 下为最小高度） | `1440` |
| `--max-height` |  | `dynamic` 最大高度 | `4320` |
| `--dpr` |  | 设备像素比（清晰度） | `2` |

#### 排版主题（`--theme`）

- `playful-geometric`：活泼几何（Memphis）**（默认推荐）**
- `default`：简约浅灰渐变背景（`#f3f3f3 -> #f9f9f9`）
- `neo-brutalism`：新粗野主义
- `botanical`：植物园自然
- `professional`：专业商务
- `retro`：复古怀旧
- `terminal`：终端命令行
- `sketch`：手绘素描

#### 分页模式（`--mode`）

- `separator`：按 `---` 分隔符分页（适合内容已手动控量）
- `auto-fit`：固定尺寸下自动缩放文字，避免溢出/留白（适合封面+单张图片但尺寸固定的情况）
- `auto-split`：按渲染后高度自动切分分页（适合切分不影响阅读的长文内容）
- `dynamic`：根据内容动态调整图片高度（注意：图片最高 4320，字数超过 550 的不建使用此模式）

#### 常用示例

```bash
# 1) 推荐：playful-geometric 主题 + 手动分隔分页
python scripts/render_xhs.py output/my-topic/content.md -o output/my-topic/ -t playful-geometric -m separator

# 2) 使用 Node.js 渲染
node scripts/render_xhs.js output/my-topic/content.md -o output/my-topic/ -t playful-geometric -m separator

# 3) 固定 1080x1440，自动缩放文字
python scripts/render_xhs.py content.md -m auto-fit

# 4) 自动切分分页（推荐：内容长短不稳定）
python scripts/render_xhs.py content.md -m auto-split

# 5) 动态高度
python scripts/render_xhs.py content.md -m dynamic --max-height 4320
```

Node.js 参数与 Python 基本一致：`--output-dir/-o`、`--theme/-t`、`--mode/-m`、`--width/-w`、`--height`、`--max-height`、`--dpr`。

### 第四步：发布小红书笔记（可选）

使用发布脚本将生成的图片发布到小红书：

```bash
# 必须使用 venv
.venv/bin/python scripts/publish_xhs.py --title "笔记标题" --desc "笔记描述" --images output/<slug>/cover.png output/<slug>/card_1.png output/<slug>/card_2.png
```

**前置条件**：

1. 需配置小红书 Cookie：
```
XHS_COOKIE=your_cookie_string_here
```

2. Cookie 获取方式：
   - 在浏览器中登录小红书（https://www.xiaohongshu.com）
   - 打开开发者工具（F12）
   - 在 Network 标签中查看请求头的 Cookie

## 图片规格说明

### 封面卡片
- 尺寸比例：3:4（小红书推荐比例）
- 基准尺寸：1080×1440px
- 包含：Emoji 装饰、大标题、副标题
- 样式：渐变背景 + 圆角内容区

### 正文卡片
- 尺寸比例：3:4
- 基准尺寸：1080×1440px
- 支持：标题、段落、列表、引用、代码块、图片
- 样式：白色卡片 + 渐变背景边框

## 技能资源

### 脚本文件
- `scripts/render_xhs.py` - Python 渲染脚本
- `scripts/render_xhs.js` - Node.js 渲染脚本
- `scripts/publish_xhs.py` - 小红书发布脚本

### 资源文件
- `assets/cover.html` - 封面 HTML 模板
- `assets/card.html` - 正文卡片 HTML 模板
- `assets/styles.css` - 共用样式表

## 完成后输出

渲染完成后，**必须**在最后输出该笔记子文件夹的完整 Finder 链接，方便用户一键跳转查看生成的图片：

```
📂 [点击打开笔记目录](file://<当前工作目录的绝对路径>/output/<slug>)
```

链接格式为 `file://<笔记子文件夹的绝对路径>`，使用 Markdown 超链接语法。请根据实际工作目录拼接完整路径。

## 注意事项

1. 每篇笔记的 Markdown 文件和渲染图片保存在 `output/<slug>/` 独立子文件夹中，避免覆盖
2. 技能目录仅存放脚本和模板，不存放用户数据
3. 图片尺寸会根据内容自动调整，但保持 3:4 比例
4. Cookie 有有效期限制，过期后需要重新获取
5. **Python 脚本必须使用 `.venv` 中的解释器**，系统 Python 因 macOS 限制无法直接安装依赖
6. **渲染**有两种方式：Node.js（推荐，无需 venv）或 Python（需要 venv）
7. **发布**只有 Python 版本，必须使用 venv
