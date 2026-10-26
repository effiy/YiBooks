# CLAUDE.md — YiBooks

> 个人电子书库。EPUB 格式电子书的存储、管理与阅读辅助。通过 Claude Code 进行电子书内容分析、格式转换、知识提取和阅读笔记管理。

---

## 项目概况

| 维度 | 值 |
|------|-----|
| 名称 | YiBooks |
| 类型 | 内容管理（电子书库） |
| 格式 | EPUB（主要） |
| 用途 | 个人阅读、知识管理、AI 辅助学习 |
| 关联项目 | YiKnowledge（知识库，可导入书中精华） |
| 目录 | `/Users/yi/YrY/YiBooks/` |

## 项目结构

```
YiBooks/
├── *.epub                    # EPUB 电子书文件
│   ├── Agent设计模式.epub     # AI Agent 设计模式
│   ├── CCHarness.epub         # Claude Code Harness
│   ├── 和孩子沟通.epub         # 亲子沟通
│   ├── 父与子.epub            # 经典漫画
│   ├── 项目管理.epub          # 项目管理知识
│   ├── 搜索力.epub            # 搜索技巧
│   ├── 西蒙学习法.epub        # 学习方法论
│   ├── 小学生学英语.epub       # 小学英语学习
│   └── 小学英语.epub          # 小学英语教材
└── CLAUDE.md                 # 本文件
```

## 核心操作

### EPUB 文件处理

```bash
# 查看 EPUB 结构（EPUB 本质是 ZIP）
unzip -l file.epub | head -30

# 提取纯文本内容
python3 -c "
import zipfile, re
with zipfile.ZipFile('file.epub') as z:
    for name in z.namelist():
        if name.endswith('.xhtml') or name.endswith('.html'):
            text = z.read(name).decode('utf-8')
            clean = re.sub('<[^>]+>', ' ', text)
            print(clean[:500])
"

# 提取到独立目录
mkdir -p extracted/book-name
unzip -o file.epub -d extracted/book-name/
```

### 格式转换

```bash
# EPUB → Markdown（使用 pandoc）
pandoc input.epub -o output.md --wrap=none

# EPUB → 纯文本
pandoc input.epub -o output.txt --to=plain

# 批量转换
for f in *.epub; do
    pandoc "$f" -o "${f%.epub}.md" --wrap=none
done
```

### AI 辅助阅读工作流

1. **快速概览** — 提取目录和前言，了解全书框架
2. **深度阅读** — 按章节逐步分析，生成笔记
3. **知识提取** — 提炼关键概念、模型、方法，导入 YiKnowledge
4. **交叉引用** — 多本书之间关联概念

## 数据流

```
EPUB 文件
  │ unzip / pandoc
  ▼
纯文本 / Markdown
  │ Claude Code 分析
  ▼
结构化笔记（关键概念、模型、引用）
  │ 导入
  ▼
YiKnowledge（知识库持久化）
  │ 查询
  ▼
YiAi（AI 检索增强）
```

## 项目约束

| 规则 | 说明 |
|------|------|
| 文件格式 | 优先使用 EPUB；PDF 可转换为 EPUB 再处理 |
| 版权尊重 | 仅限个人学习使用，不对外分发内容 |
| 元数据记录 | 新增电子书记录书名、作者、来源、添加日期 |
| 笔记格式 | Markdown 格式，存于 YiKnowledge（非本目录） |
| 批量处理 | 大规模提取使用脚本，避免手动重复操作 |
| 命名规范 | 文件名使用书籍中文标题，避免特殊字符 |

## 近期分析任务

### Agent设计模式.epub

- 提取 Agent 设计模式分类体系
- 对比 claude-skills 中的 agent 实现
- 整理可应用的工程化 Agent 模式

### 项目管理.epub

- 提取 PMBOK 核心框架
- 与 project-management/ 技能域交叉验证
- 生成项目健康检查清单

## 参考指引

| 资源 | 用途 |
|------|------|
| [YiKnowledge](../YiKnowledge/CLAUDE.md) | 知识库管理，笔记持久化目标 |
| [YiAi](../YiAi/CLAUDE.md) | AI 后端服务，提供分析能力 |
| [claude-skills](../claude-skills/CLAUDE.md) | 技能库，可用的领域知识技能 |
| [pandoc 文档](https://pandoc.org/) | 电子书格式转换工具 |
| [calibre](https://calibre-ebook.com/) | 电子书管理软件（备用） |

## 操作命令

本项目无代码构建。操作命令：

```bash
# 列出所有 EPUB 及文件大小
ls -lh *.epub

# 统计书籍数量
ls *.epub | wc -l

# 批量提取元数据（需安装 ebook-meta，来自 calibre）
for f in *.epub; do
    echo "=== $f ==="
    ebook-meta "$f" 2>/dev/null | head -5
done

# 生成书籍清单 Markdown
echo "# YiBooks 书目清单" > BOOKLIST.md
echo "" >> BOOKLIST.md
for f in *.epub; do
    size=$(ls -lh "$f" | awk '{print $5}')
    name="${f%.epub}"
    echo "- **${name}** (${size})" >> BOOKLIST.md
done
```