# 小白读论文 (Paper for Beginners)

将学术论文转化为零基础读者也能看懂的通俗解读。用大白话、生活比喻和故事化叙述，让任何人都能读懂论文。

## 特性

- 10 个结构化 Section，从基本信息到动手实践，全方位解读论文
- 每个抽象概念都配生活化比喻，零门槛理解
- 自动搜索论文全文和开源代码
- 可选：一键保存到 Notion，排版精美

## 安装

### 方式一：通过 Claude Code Plugin 安装（推荐）

```bash
# 1. 添加 marketplace
/plugin marketplace add MRT-8/paper-for-beginners

# 2. 安装插件
/plugin install paper-for-beginners@paper-for-beginners
```

### 方式二：手动安装

将 `skills/paper-for-beginners/` 目录复制到 `~/.claude/skills/` 下：

```bash
git clone https://github.com/MRT-8/paper-for-beginners.git
cp -r paper-for-beginners/skills/paper-for-beginners ~/.claude/skills/
```

## 使用方式

安装后，在 Claude Code 中直接说：

- "小白读论文 Attention Is All You Need"
- "用大白话讲讲这篇论文 https://arxiv.org/abs/xxxx.xxxxx"
- "帮我看懂这篇论文"
- "ELI5 this paper"

## 输出结构

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

## Notion 集成（可选）

如果你希望将解读结果自动保存到 Notion：

1. 创建 [Notion Integration](https://www.notion.so/my-integrations) 并获取 Token
2. 设置环境变量：
   ```bash
   export NOTION_TOKEN="your_notion_integration_token"
   ```
3. 在 Notion 中创建一个用于存放论文解读的页面，获取其 Page ID
4. 将 `SKILL.md` 中第四步的 `<YOUR_NOTION_PAGE_ID>` 替换为你的实际 Page ID

## License

[Apache License 2.0](LICENSE)
