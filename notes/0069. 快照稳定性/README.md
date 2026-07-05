# [0069. 快照稳定性](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0069.%20%E5%BF%AB%E7%85%A7%E7%A8%B3%E5%AE%9A%E6%80%A7)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 随机值问题](#3-随机值问题)
- [4. 使用 expect.any()](#4-使用-expectany)
- [5. 时间值处理](#5-时间值处理)
- [6. 平台差异](#6-平台差异)
- [7. 排除动态字段](#7-排除动态字段)
- [8. 使用 expect.objectContaining](#8-使用-expectobjectcontaining)
- [9. 正则表达式过滤](#9-正则表达式过滤)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 随机值导致快照不稳定
- 使用 expect.any() 匹配任意值
- 时间值处理（Mock 时间）
- 平台差异处理
- 排除动态字段
- 正则表达式过滤

## 2. 评价

快照稳定性是快照测试的关键问题。随机值和时间值需要特殊处理，使用 expect.any() 或 Mock 时间确保快照一致。

## 3. 随机值问题

如果输出包含随机值（如 UUID），快照每次都会不同：

```typescript
// 不稳定的快照
test('unstable', () => {
  const result = {
    id: crypto.randomUUID(), // 每次不同
    createdAt: new Date(), // 每次不同
  }
  expect(result).toMatchSnapshot()
})
```

## 4. 使用 expect.any()

用 `expect.any()` 匹配任意值：

```typescript
test('stable', () => {
  const result = {
    id: crypto.randomUUID(),
    createdAt: new Date(),
  }
  expect(result).toMatchObject({
    id: expect.any(String),
    createdAt: expect.any(Date),
  })
})
```

## 5. 时间值处理

```typescript
// Mock 时间
vi.useFakeTimers()
vi.setSystemTime(new Date('2024-01-01'))

test('stable time', () => {
  const result = {
    timestamp: Date.now(),
  }
  expect(result).toMatchSnapshot()

  vi.useRealTimers()
})
```

## 6. 平台差异

不同操作系统可能产生不同的输出（如换行符、路径分隔符）：

```typescript
// 处理路径差异
test('path', () => {
  const result = path.join('a', 'b')
  expect(result).toMatchInlineSnapshot(`"a/b"`)
})
```

## 7. 排除动态字段

```typescript
test('exclude dynamic', () => {
  const result = {
    id: 1,
    name: 'test',
    updatedAt: new Date().toISOString(),
  }

  const { updatedAt, ...stable } = result
  expect(stable).toMatchSnapshot()
})
```

## 8. 使用 expect.objectContaining

```typescript
test('partial match', () => {
  const result = getUser()
  expect(result).toMatchObject({
    name: 'Alice',
    age: expect.any(Number),
  })
})
```

## 9. 正则表达式过滤

```typescript
test('regex filter', () => {
  const log = generateLog()
  const cleaned = log.replace(/\d{4}-\d{2}-\d{2}/, 'DATE')
  expect(cleaned).toMatchSnapshot()
})
```

## 10. 注意事项

- 随机值会导致快照不稳定
- 时间值需要 Mock 或过滤
- 平台差异需要处理
- 建议只快照稳定的输出
