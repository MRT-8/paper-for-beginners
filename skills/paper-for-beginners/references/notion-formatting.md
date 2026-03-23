# Notion 页面格式化参考

写入 Notion 页面时的 API 调用规范和 block 结构参考。

## API 调用方式

使用 `curl` 直接调用 Notion API（MCP 工具只支持 paragraph/bulleted_list_item，排版差）。

```bash
curl -s -X PATCH "https://api.notion.com/v1/blocks/{page_id}/children" \
  -H "Authorization: Bearer $NOTION_TOKEN" \
  -H "Notion-Version: 2022-06-28" \
  -H "Content-Type: application/json" \
  -d @/tmp/notion_blocks.json
```

- `$NOTION_TOKEN` 从环境变量获取，禁止硬编码
- 每次最多 100 个 children blocks，超过需分批
- 先写入临时 JSON 文件，再用 `curl -d @file.json` 发送
- 每批写入后用 `python3 -c` 解析响应确认成功

## Toggleable Heading 嵌套规则

`is_toggleable: true` 的 heading 是容器型 block，子内容必须放在 `children` 数组内部。`heading_2` 同理。

正确写法：
```json
{
  "type": "heading_1",
  "heading_1": {
    "rich_text": [{"type": "text", "text": {"content": "标题文字"}}],
    "color": "default",
    "is_toggleable": true,
    "children": [
      {"type": "paragraph", "paragraph": {"rich_text": [{"type": "text", "text": {"content": "这段内容在折叠标题内部"}}]}},
      {"type": "bulleted_list_item", "bulleted_list_item": {"rich_text": [{"type": "text", "text": {"content": "列表项也在内部"}}]}}
    ]
  }
}
```

错误写法（子内容变成同级 block，不会出现在折叠内部）：
```json
{"type": "heading_1", "heading_1": {"rich_text": [...], "is_toggleable": true}},
{"type": "paragraph", "paragraph": {...}}
```

## 页面整体结构

顶层 block 序列：`callout(基本信息)` → `quote(摘要翻译)` → `divider` → `heading_1(Section2)` → `divider` → `heading_1(Section3)` → ... → `heading_1(Section10)`

每个 `heading_1` 的内容通过 `children` 嵌套在内部，`divider` 放在顶层作为 section 间分隔。

## Section 1：论文基本信息

由两个顶层 block 组成：`callout`（基本信息）+ `quote`（摘要翻译）。

**Block 1 — 基本信息 callout**（blue_background，icon 📋）：
```json
{
  "type": "callout",
  "callout": {
    "icon": {"type": "emoji", "emoji": "📋"},
    "color": "blue_background",
    "rich_text": [
      {"type": "text", "text": {"content": "标题"}, "annotations": {"bold": true}},
      {"type": "text", "text": {"content": "：英文标题\n"}},
      {"type": "text", "text": {"content": "中文"}, "annotations": {"bold": true}},
      {"type": "text", "text": {"content": "：中文标题\n"}},
      {"type": "text", "text": {"content": "作者"}, "annotations": {"bold": true}},
      {"type": "text", "text": {"content": "：作者（机构）\n"}},
      {"type": "text", "text": {"content": "发表"}, "annotations": {"bold": true}},
      {"type": "text", "text": {"content": "：年份 / venue\n"}},
      {"type": "text", "text": {"content": "论文链接"}, "annotations": {"bold": true}},
      {"type": "text", "text": {"content": "："}},
      {"type": "text", "text": {"content": "链接文字", "link": {"url": "URL"}}},
      {"type": "text", "text": {"content": "\n"}},
      {"type": "text", "text": {"content": "代码链接"}, "annotations": {"bold": true}},
      {"type": "text", "text": {"content": "："}},
      {"type": "text", "text": {"content": "GitHub", "link": {"url": "URL"}}}
    ]
  }
}
```

**Block 2 — 摘要翻译 quote**（quote 样式天然适合"引用原文翻译"，避免 callout 过长）：
```json
{
  "type": "quote",
  "quote": {
    "rich_text": [
      {"type": "text", "text": {"content": "摘要翻译"}, "annotations": {"bold": true}},
      {"type": "text", "text": {"content": "：摘要的中文翻译内容……"}}
    ],
    "color": "default"
  }
}
```

## Section 2-10：Toggleable Heading 子内容规格

| Section | Emoji + 标题 | children 内容 |
|---------|-------------|--------------|
| 2 | 🎯 一句话看懂 + 速记版流程 | `callout`(yellow_background, 💡) 一句话概括 + `heading_3`("速记版流程") + `numbered_list_item` 列出3-5步pipeline |
| 3 | 🔑 专业术语词典 | 多个 `bulleted_list_item`（bold术语名 + 解释） |
| 4 | 🔍 这个研究为什么要做 | `callout`(orange_background, ❓) 一句话点明核心动机 + `heading_3`(旧方法痛点/灵感来源/核心假设) + `paragraph` 或 `bulleted_list_item` 展开 |
| 5 | 🏗️ 方法流程详解 | `numbered_list_item` 逐步描述（吃进什么→做了什么→吐出什么）；如有公式用 `callout`(gray_background, 📐) 包裹人话翻译 |
| 6 | ⚔️ 和别的方法比有什么不同 | `paragraph` 概述核心区别 + `callout`(purple_background, ✨) 创新点 + table block 做对比表（见下方模板）+ `paragraph` 适用场景 |
| 7 | 📊 实验怎么做的、效果如何 | `callout`(green_background, 🏆) 最亮眼结果一句话 + `heading_3`(实验设计/关键结果/最强场景/实验局限) + `bulleted_list_item`，关键数字用 bold+red 高亮 |
| 8 | 📚 故事版本 | `heading_3` 分段 + `paragraph` |
| 9 | ⚖️ 论文亮点与不足 | `heading_3`(✅亮点/⚠️不足) + `bulleted_list_item`（不足侧重方法层面的设计缺陷和理论局限，不重复 Section 7 的实验局限） |
| 10 | 🚀 读完应该记住的 | `numbered_list_item` takeaway + `callout`(yellow_background, ⭐) 读后收获一句话（侧重价值和启发）+ `heading_3`("想动手试试？") + `bulleted_list_item`（开源情况、复现步骤、超参数建议、迁移可能性） |

## Section 6：对比表 Table Block 模板

Notion 原生 table block 的 JSON 结构（3列示例）：
```json
{
  "type": "table",
  "table": {
    "table_width": 3,
    "has_column_header": true,
    "has_row_header": true,
    "children": [
      {
        "type": "table_row",
        "table_row": {
          "cells": [
            [{"type": "text", "text": {"content": "对比项"}}],
            [{"type": "text", "text": {"content": "旧方法名"}}],
            [{"type": "text", "text": {"content": "本文方法名"}}]
          ]
        }
      },
      {
        "type": "table_row",
        "table_row": {
          "cells": [
            [{"type": "text", "text": {"content": "优点"}, "annotations": {"bold": true}}],
            [{"type": "text", "text": {"content": "旧方法优点"}}],
            [{"type": "text", "text": {"content": "新方法优点"}}]
          ]
        }
      },
      {
        "type": "table_row",
        "table_row": {
          "cells": [
            [{"type": "text", "text": {"content": "缺点"}, "annotations": {"bold": true}}],
            [{"type": "text", "text": {"content": "旧方法缺点"}}],
            [{"type": "text", "text": {"content": "新方法缺点"}}]
          ]
        }
      },
      {
        "type": "table_row",
        "table_row": {
          "cells": [
            [{"type": "text", "text": {"content": "改进点"}, "annotations": {"bold": true}}],
            [{"type": "text", "text": {"content": "—"}}],
            [{"type": "text", "text": {"content": "具体改进描述"}}]
          ]
        }
      }
    ]
  }
}
```

注意：table block 的 `children`（table_row）必须在创建时一次性提供，不能后续追加。

## 排版规范

- heading / paragraph 用 `"color": "default"`，不加背景色
- 每个 Section 的 callout 颜色各不相同，增强视觉区分：

| callout 用途 | 背景色 | icon | 出现位置 |
|-------------|--------|------|---------|
| 论文基本信息 | `blue_background` | 📋 | Section 1 |
| 核心一句话概括 | `yellow_background` | 💡 | Section 2 |
| 研究动机提炼 | `orange_background` | ❓ | Section 4 |
| 公式人话翻译 | `gray_background` | 📐 | Section 5 |
| 创新点高亮 | `purple_background` | ✨ | Section 6 |
| 最亮眼实验结果 | `green_background` | 🏆 | Section 7 |
| 读后收获总结 | `yellow_background` | ⭐ | Section 10 |

- 关键词：`"annotations": {"bold": true}`
- 重点高亮：`"annotations": {"color": "red"}` 或 `"color": "yellow_background"`
- 英文术语：`"annotations": {"code": true}`
- 链接：rich_text 的 `"link": {"url": "..."}` 属性

## 写入后验证

第一个 batch 写入后，检查响应中 toggleable heading 的 `has_children` 是否为 `true`，确认嵌套正确后再继续后续 batch。
