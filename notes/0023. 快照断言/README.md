# [0023. 快照断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0023.%20%E5%BF%AB%E7%85%A7%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. toMatchSnapshot 基础](#3-tomatchsnapshot-基础)
- [4. 更新快照](#4-更新快照)
- [5. toMatchInlineSnapshot](#5-tomatchinlinesnapshot)
- [6. 快照适用场景](#6-快照适用场景)
- [7. 快照的维护成本](#7-快照的维护成本)
- [8. 快照中的动态值](#8-快照中的动态值)
- [9. 快照序列化器](#9-快照序列化器)

<!-- endregion:toc -->

## 1. 本节内容

- toMatchSnapshot 外部快照
- 更新快照（vitest -u）
- toMatchInlineSnapshot 内联快照
- 快照适用场景
- 快照维护成本
- 动态值处理

## 2. 评价

快照测试适合捕获复杂数据结构的变化。内联快照可读性更好，但会增加文件体积。需要定期维护快照，避免盲目更新。

## 3. toMatchSnapshot 基础

`toMatchSnapshot` 将值序列化后保存到 `.snap` 文件，后续运行时对比：

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

首次运行时会在 `__snapshots__/` 目录下生成 `.snap` 文件，包含序列化后的值。后续运行会自动对比，不一致则测试失败。

## 4. 更新快照

当输出确实发生变化时，更新快照：

```bash
# 更新所有快照
vitest run -u

# 交互式更新
vitest run --update
```

## 5. toMatchInlineSnapshot

`toMatchInlineSnapshot` 将快照内容直接写入测试文件：

```typescript
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

首次运行时 Vitest 会自动填充快照内容。后续运行时对比内联内容。

内联快照的优点：

- 快照内容直接可见，不需要跳转到 `.snap` 文件
- 适合小型数据结构

## 6. 快照适用场景

```typescript
// API 响应快照
test('API response', async () => {
  const data = await fetchUser(1)
  expect(data).toMatchSnapshot()
})

// 组件渲染快照
test('component render', () => {
  const html = renderComponent()
  expect(html).toMatchSnapshot()
})

// 复杂数据结构
test('computed result', () => {
  const result = processLargeDataset(input)
  expect(result).toMatchSnapshot()
})
```

## 7. 快照的维护成本

快照测试需要定期维护：

- 代码变更导致输出变化时，需要更新快照
- 快照文件需要提交到版本控制
- 团队成员需要审查快照变更（diff）
- 大型快照难以阅读和理解

建议对稳定的核心输出使用快照，对频繁变化的数据使用具体断言。

## 8. 快照中的动态值

如果输出包含随机值（如 UUID、时间戳），快照每次都会不同：

```typescript
test('snapshot with dynamic value', () => {
  const result = {
    id: crypto.randomUUID(),
    createdAt: new Date().toISOString(),
  }

  // 使用 expect.any() 匹配任意值
  expect(result).toMatchObject({
    id: expect.any(String),
    createdAt: expect.any(String),
  })
})
```

## 9. 快照序列化器

可以自定义快照的序列化方式：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    snapshotSerializers: ['my-custom-serializer.ts'],
  },
})
```
