---
name: jp-goods-pricing-table
description: "extract merchandise information from uploaded japanese goods images and convert it into a clean pricing table. use when a user shares one or more product lineup images, event goods posters, capsule sheets, or merch flyers and wants: (1) item names extracted from japanese, (2) concise chinese item names, (3) prices organized into a table, (4) random or blind-pack items labeled, or (5) proxy purchase reference prices calculated from a user-provided or default exchange rate."
---

# jp goods pricing table

Convert uploaded Japanese merchandise lineup images into a structured table for resale, ordering, or cataloging.

## workflow

1. Read every uploaded image and identify each product card or product block.
2. Extract the visible item number, original Japanese item name, and listed price.
3. Rewrite the item name into a concise Chinese short name.
4. Detect whether the item is random/blind-pack style and append `（随机）` to the Chinese short name when needed.
5. Calculate a proxy purchase reference price in CNY when the user asks for it.
6. Output one consolidated markdown table unless the user asks for another format.

## extraction rules

- Prefer the product title printed next to the item number as the Japanese original name.
- Keep Japanese names exactly as printed when legible. Do not romanize.
- Use the visible item number from the image. If the sheet restarts numbering across pages, preserve the printed numbering rather than renumbering.
- Keep the listed selling price in JPY exactly as shown, including `¥` and tax-included wording only if it matters to the user.
- When a product is clearly a prize, lottery result, bonus, or non-priced award, leave the price as shown in context and do not invent a standalone sale price.
- If part of the text is too small or ambiguous, make the best grounded reading from the image and state briefly that uncertain entries may need manual review.

## chinese short name rules

Translate into short, natural Chinese for sales tables.

- Prefer concise product nouns such as `T恤`, `宽版T恤`, `亚克力立牌`, `徽章`, `全息徽章`, `挂件`, `钥匙扣`, `色纸`, `文件夹`, `海报`, `毛巾`, `外套`, `托特包`.
- Keep important differentiators such as band name, version, sideA/sideB, vol.2, character group, event title, or material when needed to distinguish items.
- Avoid over-translating slogans or long subtitle text unless it helps identify the item.
- For sets, use concise wording like `套装`, `组合`, `相框+照片套装`.
- For gacha/capsule items, keep the series identity in the short name when it helps users sell or sort the item.

## random item rules

Append `（随机）` to the Chinese short name when the item is visibly random or blind-pack style.

Treat the item as random when the image or name includes cues such as:
- `トレーディング`
- `ランダム`
- `全種ランダム`
- `blind`
- `trading`
- `BOX`
- capsule or gacha assortment wording
- lottery-style single-draw prize pools where the specific design is not chosen

Do not add `（随机）` when the item is a normal single-design product, even if multiple variants are shown on the sheet and the customer can choose.

## proxy price calculation

Only calculate this column when the user asks for proxy purchase pricing.

Default rule:
- Use the user-provided exchange rate if they specify one.
- Otherwise use a default rate of `0.050`.
- Formula: `JPY price × exchange rate`.
- Round any non-integer result upward to the next whole CNY value.

If the user gives special overrides for certain item categories, follow those overrides instead of the default rate.

## default output format

Use this markdown table unless the user asks for another format.

| 编号 | 商品原名（日文） | 中文简称 | 定价 | 代购价格（参考） |
|------|------------------|----------|------|------------------|
| 1 | Tシャツ | T恤 | ¥4,400 | ¥220 |

## output notes

- If the user only asks for extraction and not pricing conversion, omit `代购价格（参考）`.
- If the user asks to merge multiple images, combine them into one table.
- If the sheet contains many sub-variants under one product number, keep the parent product as one row unless the user explicitly asks to split by variant.
- Keep explanations short. Put the table first.
- After the table, optionally add one short note only when needed, such as uncertain OCR, random-item judgment, or a special rate exception.

## examples

### example 1
User asks: `帮我提取这里面的商品名称，整理成一个表格，包含商品编号，商品原名，中文简称，定价`

Return a 4-column table:
- 编号
- 商品原名（日文）
- 中文简称
- 定价

### example 2
User asks: `帮我补充代购价格，统一 x 0.048，有小数向上取整`

Return a 5-column table and calculate:
- 代购价格（参考） = JPY × 0.048, rounded up

### example 3
User asks: `简介中如果有随机请放在简称后面`

Detect random/blind-pack items and write names like:
- `徽章（随机）`
- `亚克力卡片（随机）`
- `场景块 vol.2（随机）`

### example 4
User uploads several pages from a live goods lineup.

Combine all pages into one consolidated table, preserve printed item numbers, and avoid duplicate rows.
