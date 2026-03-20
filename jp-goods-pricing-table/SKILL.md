---
name: jp-goods-pricing-table
description: extract merchandise information from uploaded japanese goods images and convert it into a clean pricing table. use when a user shares one or more product lineup images, event goods posters, capsule sheets, merch flyers, or store goods screenshots and wants japanese item names extracted, concise chinese item names, random-item labeling, merged merchandise tables, or proxy purchase reference prices calculated from a provided or default exchange rate.
---

# jp-goods-pricing-table

Extract item information from uploaded Japanese merchandise images and output a clean markdown pricing table for proxy buying, resale listing, and cataloging.

## Core task

Read one or more uploaded Japanese merchandise images and convert the visible sale items into a structured table.

Supported output fields:

- item number
- original Japanese item name
- concise Chinese item name
- JPY list price
- optional proxy reference price

Default output format is a markdown table.

## Output columns

Use these columns by default:

| 编号 | 商品原名（日文） | 中文简称 | 定价 |

If the user asks for proxy pricing, add:

| 编号 | 商品原名（日文） | 中文简称 | 定价 | 代购价格（参考） |

Do not add extra columns unless the user asks.

## Extraction rules

For each visible merchandise entry:

1. Extract the printed item number if present.
2. Extract the original Japanese item name as shown.
3. Extract the visible sale price in JPY.
4. Generate a short, natural Chinese item name suitable for resale sheets or catalog tables.

Rules:

- Preserve Japanese item names as shown when possible.
- Do not romanize Japanese names.
- Use the printed numbering from the image when available.
- Do not invent numbering.
- Do not rewrite item ordering unless the user asks for reordering.
- Prefer the official item label printed next to the product entry over surrounding promotional copy.

## Chinese item name rules

Generate concise Chinese names that are easy to read in sales tables.

Good examples:

- T恤
- 宽版T恤
- 亚克力立牌
- 亚克力挂件
- 徽章
- 全息徽章
- 色纸
- 文件夹
- 海报
- 毛巾
- 托特包
- 钥匙扣
- 卡套
- 贴纸套装

Guidelines:

- Keep names short and practical.
- Preserve meaningful distinctions such as member name, version, vol.2, material, size, or format when needed.
- Avoid translating slogans, subtitles, or marketing copy unless needed to distinguish products.
- For set or bundle items, use short labels such as `套装` or `组合`.
- Keep wording natural for Chinese merchandise selling contexts.

## Random item labeling

If an item is clearly random, blind-packed, trading, box-based, or gacha-like, append:

`（随机）`

Common indicators include:

- トレーディング
- ランダム
- 全種ランダム
- blind
- trading
- BOX
- capsule
- gacha

Examples:

- 徽章（随机）
- 亚克力卡片（随机）
- 场景块 vol.2（随机）

Do not mark an item as random if:

- it merely has multiple selectable variants
- the buyer can choose the design directly
- the image shows a lineup of variants without random-sale wording

## Proxy price calculation

Only add `代购价格（参考）` when the user asks for proxy pricing.

Rules:

- If the user provides an exchange rate, use that rate.
- Otherwise use the default rate: `0.050`.
- Formula: `JPY price × exchange rate`
- If the result has decimals, round up to the next integer.
- Output the proxy price as an integer RMB-style reference value with `¥`.

Examples:

- `¥4,400 × 0.050 = ¥220`
- `¥3,300 × 0.048 = 158.4` -> `¥159`

If the user provides a custom pricing rule, custom multiplier, or category-based pricing rule, follow the user's rule instead of the default.

## Multi-image handling

When the user uploads multiple images:

- Merge them into one combined table unless the user asks for separate tables.
- Preserve original printed numbering from each image.
- Avoid duplicate rows.
- If the same item appears across multiple images, keep one row unless separate rows are explicitly requested.
- If one image has missing fields and another image provides the missing details for the same item, merge them into a single more complete row.
- Keep parent items as one row by default when multiple variants belong to the same merchandise entry.
- Only split variants into separate rows if the user explicitly asks.

## Duplicate and repeat-item rules

When repeated entries appear:

- Do not duplicate rows for the same sale item across multiple uploaded images.
- Use the clearest and most complete visible version of the repeated item.
- If two repeated entries conflict, prefer the clearer image and mention the ambiguity briefly if needed.
- If the user wants a per-image inventory list rather than a deduplicated master table, follow the user’s instruction.

## Price extraction rules

Use the most directly associated visible sale price for the item.

Rules:

- Prefer the price printed in the same item block.
- If both tax-exclusive and tax-inclusive prices are shown, prefer the tax-inclusive price unless the user asks otherwise.
- If both single-item price and box price are shown, use the price that matches the extracted item row.
- Do not guess prices from nearby unrelated items.
- Preserve the original JPY price format in the output as `¥x,xxx` when possible.

## Exclusions

Exclude these by default unless the user explicitly asks to include them:

- purchase bonuses
- store benefits
- preorder benefits
- prizes
- non-sale items
- display-only items
- non商品 explanatory text

If unsure whether an entry is a normal sale item or a bonus item:

- prefer excluding it
- mention the ambiguity briefly after the table if useful

## Uncertainty handling

When any field is unreadable, cropped, ambiguous, or too blurry:

- do not guess
- do not fabricate names, prices, or numbering
- use `待确认` only when keeping the row is still useful
- otherwise omit the row if the item cannot be reliably identified as a sale item
- add one short review note after the table if manual verification is needed

Examples of acceptable notes:

- 部分小字较模糊，个别条目建议人工复核
- 个别价格区域不清晰，需以原图确认

## Formatting requirements

Always output a clean markdown table.

Requirements:

- Keep one item per row.
- Keep Japanese names in the `商品原名（日文）` column.
- Keep concise Chinese names in the `中文简称` column.
- Show prices in `¥` format.
- Do not include long explanations before the table unless the user asks.
- If there are important ambiguities, keep the note after the table short.

## Preferred behavior

Optimize for a practical merchandise table, not a raw OCR transcript.

Priorities:

1. usability
2. naming consistency
3. pricing clarity
4. correct random-item labeling
5. stable multi-image merging

## Example requests

User:
帮我提取这张图里的商品，整理成表格，包含编号、商品原名、中文简称和定价

Assistant:
Return a markdown table with:
- 编号
- 商品原名（日文）
- 中文简称
- 定价

User:
再帮我按 0.048 算代购价格，有小数向上取整

Assistant:
Add:
- 代购价格（参考）

User:
如果是随机商品，请在简称后面标注随机

Assistant:
Append `（随机）` to applicable Chinese item names only.

User:
把这几张物贩图合并成一个总表

Assistant:
Merge into one deduplicated table while preserving printed numbering where available.
