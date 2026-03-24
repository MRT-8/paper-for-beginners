# 小白读论文 Paper for Beginners

> 看不懂论文？丢给 Claude，它用大白话、生活比喻和故事帮你把论文"翻译"成人话。

一个 [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) 的 [Agent Skill](https://agentskills.io/)，将学术论文转化为零基础读者也能看懂的结构化通俗解读。

## 为什么需要这个 Skill

读论文的典型痛点：

- 摘要看完不知道论文在讲什么
- 专业术语太多，每个词都要查
- 方法部分像天书，公式看不懂
- 读完了记不住重点

**这个 Skill 让 Claude 帮你做一件事：把论文拆成 10 个结构化 Section，用讲故事的方式让你真正读懂它。**

## Demo

以经典论文 *Attention Is All You Need* 为例，以下是 Skill 实际输出的片段：

### Section 2 — 一句话看懂 + 速记版流程

> **一句话概括**：这篇论文提出了一种新的神经网络架构，不用传统的"逐字阅读"方式，而是让模型一次性"看到"整段话的所有位置，靠注意力打分决定哪里重要。
>
> **速记版流程**：
> 1. 把句子里的每个词变成一串数字
> 2. 每个词问其他所有词"你跟我相关吗？"，得到一组关注度分数
> 3. 按分数把相关信息加权汇总，形成新的表示
> 4. 重复这个过程好多层，理解越来越深
> 5. 最后根据理解生成翻译结果

### Section 3 — 专业术语词典（节选）

> **Attention 机制** — 让模型学会"重点看哪里"。就像你读长文章时眼睛会自动跳到加粗关键词上，Attention 就是教 AI 做同样的事。
>
> **Self-Attention** — "自己看自己"。一句话里的每个词去跟同一句话里的其他词互相打分，看彼此有多相关。比如"小猫追着球跑"，"追"会特别关注"小猫"和"球"。
>
> **Multi-Head Attention** — 同时从多个角度去"看"。一组 Attention 只能捕捉一种关系，多组并行就像多个侦探各查一条线索，最后汇总。

## 输出结构

每篇论文解读包含 10 个 Section：

| Section | 内容 |
|---------|------|
| 1. 论文基本信息 | 标题、作者、venue、链接 + 摘要中文翻译 |
| 2. 一句话看懂 | 零术语概括 + 3-5 步速记流程 |
| 3. 专业术语词典 | 8-15 个关键术语的大白话解释 |
| 4. 为什么要做这个研究 | 痛点 → 灵感 → 核心假设 |
| 5. 方法流程详解 | 吃进什么 → 做了什么 → 吐出什么 |
| 6. 和别的方法有什么不同 | 创新点 + 对比表 |
| 7. 实验效果 | 关键结果 + 最强场景 + 局限 |
| 8. 故事版本 | 比喻故事串联核心思想 |
| 9. 亮点与不足 | 方法层面的优缺点分析 |
| 10. 读完应该记住的 | Takeaway + 动手指南 |

## 快速开始

### 安装

**方式一：Plugin 安装（推荐）**

在 Claude Code 中执行：

```
/plugin marketplace add MRT-8/paper-for-beginners
/plugin install paper-for-beginners@paper-for-beginners
```

**方式二：手动安装**

```bash
git clone https://github.com/MRT-8/paper-for-beginners.git
cp -r paper-for-beginners/skills/paper-for-beginners ~/.claude/skills/
```

### 使用

安装后，在 Claude Code 中直接说：

```
小白读论文 Attention Is All You Need
```

```
用大白话讲讲这篇论文 https://arxiv.org/abs/xxxx.xxxxx
```

```
ELI5 this paper: [论文标题]
```

## Notion 集成（可选）

支持将解读结果一键保存到 Notion，自动排版。需要：

1. 创建 [Notion Integration](https://www.notion.so/my-integrations) 并获取 Token
2. 设置环境变量 `export NOTION_TOKEN="your_token"`
3. 将 `SKILL.md` 中第四步的 `<YOUR_NOTION_PAGE_ID>` 替换为你的 Notion 页面 ID

## License

[Apache License 2.0](LICENSE)
