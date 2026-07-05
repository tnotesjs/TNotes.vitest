# [0067. 内联快照](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0067.%20%E5%86%85%E8%81%94%E5%BF%AB%E7%85%A7)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 基本用法](#3-基本用法)
- [4. 首次运行](#4-首次运行)
- [5. 与外部快照的区别](#5-与外部快照的区别)
- [6. 适用场景](#6-适用场景)
- [7. 更新内联快照](#7-更新内联快照)
- [8. 自动格式化](#8-自动格式化)
- [9. 与 Prettier 配合](#9-与-prettier-配合)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- toMatchInlineSnapshot 直接写入测试文件
- 首次运行自动填充
- 与外部快照的区别
- 适用场景
- 更新方式
- 自动格式化

## 2. 评价

内联快照将内容直接写入测试文件，可读性更好，不需要跳转到 .snap 文件。适合小型数据结构，大型数据建议使用外部快照。

## 3. 基本用法

`toMatchInlineSnapshot` 将快照内容直接写入测试文件：

```typescript
import { test, expect } from 'vitest'

test('inline snapshot', () => {
  const result = { name: 'vitest', version: 4 }
  expect(result).toMatchInlineSnapshot(`
    {
      "name": "vitest",
      "version": 4,
    }
  `)
})
```

## 4. 首次运行

首次运行时，Vitest 会自动填充快照内容到测试文件中。

## 5. 与外部快照的区别

| 维度     | 外部快照     | 内联快照       |
| -------- | ------------ | -------------- |
| 存储位置 | `.snap` 文件 | 测试文件内     |
| 可读性   | 需要跳转查看 | 直接可见       |
| 适合场景 | 大型数据     | 小型数据       |
| 维护方式 | 单独文件     | 集中在测试文件 |

## 6. 适用场景

内联快照适合小型数据结构：

```typescript
test('small data', () => {
  expect(formatDate(new Date('2024-01-01'))).toMatchInlineSnapshot(
    `"2024-01-01"`,
  )
})

test('error message', () => {
  expect(() => validate('')).toThrowErrorMatchingInlineSnapshot(
    `"Validation failed"`,
  )
})
```

## 7. 更新内联快照

```bash
# 更新所有内联快照
vitest run -u

# 更新特定文件
vitest run src/utils.test.ts -u
```

更新后，Vitest 会自动修改测试文件中的快照内容。

## 8. 自动格式化

内联快照会自动格式化，保持代码风格一致。

## 9. 与 Prettier 配合

内联快照会被 Prettier 格式化，确保代码风格统一。

## 10. 注意事项

- 内联快照会增加测试文件的大小
- 大型数据不建议使用内联快照
- 内联快照的更新会修改测试文件
