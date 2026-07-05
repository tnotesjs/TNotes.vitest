# [0066. 外部快照](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0066.%20%E5%A4%96%E9%83%A8%E5%BF%AB%E7%85%A7)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 基本用法](#3-基本用法)
- [4. .snap 文件](#4-snap-文件)
- [5. .snap 文件内容](#5-snap-文件内容)
- [6. 更新快照](#6-更新快照)
- [7. 命名快照](#7-命名快照)
- [8. 多次快照](#8-多次快照)
- [9. 提交到版本控制](#9-提交到版本控制)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- toMatchSnapshot 保存到 .snap 文件
- .snap 文件位置和内容
- 更新快照（vitest -u）
- 命名快照
- 多次快照
- 提交到版本控制

## 2. 评价

外部快照保存在 **snapshots** 目录下的 .snap 文件中，需要提交到版本控制。团队成员需要审查快照变更。

## 3. 基本用法

`toMatchSnapshot` 将快照保存到外部 `.snap` 文件：

```typescript
import { test, expect } from 'vitest'

test('snapshot', () => {
  const result = {
    name: 'vitest',
    version: 4,
    features: ['esm', 'typescript'],
  }
  expect(result).toMatchSnapshot()
})
```

## 4. .snap 文件

首次运行时，Vitest 在测试文件同目录下创建 `__snapshots__/` 目录，保存 `.snap` 文件：

```
src/
├── __snapshots__/
│   └── utils.test.ts.snap
├── utils.ts
└── utils.test.ts
```

## 5. .snap 文件内容

```typescript
// Vitest Snapshot v1
exports[`snapshot 1`] = `
{
  "name": "vitest",
  "version": 4,
  "features": [
    "esm",
    "typescript",
  ],
}
`
```

## 6. 更新快照

```bash
# 更新所有快照
vitest run -u

# 交互式更新
vitest run --update
```

## 7. 命名快照

可以为快照指定名称：

```typescript
test('named snapshot', () => {
  expect(result).toMatchSnapshot('user-profile')
})
```

## 8. 多次快照

同一测试中可以有多个快照：

```typescript
test('multiple snapshots', () => {
  expect(user).toMatchSnapshot('user')
  expect(posts).toMatchSnapshot('posts')
})
```

## 9. 提交到版本控制

`.snap` 文件应该提交到 Git：

```bash
git add src/__snapshots__/
git commit -m "test: update snapshots"
```

## 10. 注意事项

- `.snap` 文件会随测试变化而增长
- 团队成员需要审查快照变更
- 大型快照难以阅读和理解
- 建议定期清理无用的快照文件
