# [0028. 并发异步测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0028.%20%E5%B9%B6%E5%8F%91%E5%BC%82%E6%AD%A5%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 并发测试的场景](#3-并发测试的场景)
- [4. 共享状态的风险](#4-共享状态的风险)
- [5. 隔离策略](#5-隔离策略)
  - [5.1. 每个测试独立创建数据](#51-每个测试独立创建数据)
  - [5.2. 使用 describe 隔离](#52-使用-describe-隔离)
- [6. 并发测试的正确用法](#6-并发测试的正确用法)
- [7. 并发异步操作的测试](#7-并发异步操作的测试)
- [8. 并发控制](#8-并发控制)
- [9. 测试中的竞态条件](#9-测试中的竞态条件)

<!-- endregion:toc -->

## 1. 本节内容

- 并发测试场景
- 共享状态的风险
- 隔离策略（独立数据、describe 隔离）
- 并发测试正确用法
- 并发控制
- 竞态条件

## 2. 评价

并发测试需要注意共享状态的竞态条件。每个测试应独立创建数据，避免相互影响。纯函数测试适合并发，有状态的测试应串行执行。

## 3. 并发测试的场景

当测试需要并行执行多个异步操作时，需要特别注意共享状态：

```typescript
import { test, expect } from 'vitest'

test('concurrent operations', async () => {
  const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()])

  expect(users).toBeInstanceOf(Array)
  expect(posts).toBeInstanceOf(Array)
})
```

## 4. 共享状态的风险

如果多个测试并行执行且共享可变状态，会导致不确定的结果：

```typescript
// 有风险的测试
let counter = 0

test.concurrent('test A', async () => {
  counter++
  await delay(100)
  expect(counter).toBe(1) // 可能失败，因为 test B 可能同时修改了 counter
})

test.concurrent('test B', async () => {
  counter++
  await delay(100)
  expect(counter).toBe(2) // 可能失败
})
```

## 5. 隔离策略

### 5.1. 每个测试独立创建数据

```typescript
test.concurrent('test A', async () => {
  const db = await createTestDb()
  await db.insert({ name: 'Alice' })
  const users = await db.findAll()
  expect(users).toHaveLength(1)
})

test.concurrent('test B', async () => {
  const db = await createTestDb()
  await db.insert({ name: 'Bob' })
  const users = await db.findAll()
  expect(users).toHaveLength(1)
})
```

### 5.2. 使用 describe 隔离

```typescript
describe('isolated suite', () => {
  let db: TestDb

  beforeEach(async () => {
    db = await createTestDb()
  })

  test('test 1', async () => {
    await db.insert({ name: 'Alice' })
    expect(await db.count()).toBe(1)
  })

  test('test 2', async () => {
    expect(await db.count()).toBe(0) // 独立的 db 实例
  })
})
```

## 6. 并发测试的正确用法

```typescript
// 纯函数测试，无共享状态，适合并发
test.concurrent('add 1', () => {
  expect(add(1, 2)).toBe(3)
})

test.concurrent('add 2', () => {
  expect(add(0, 0)).toBe(0)
})

test.concurrent('add 3', () => {
  expect(add(-1, 1)).toBe(0)
})
```

## 7. 并发异步操作的测试

```typescript
test('parallel fetch', async () => {
  const urls = [
    'https://api.example.com/users',
    'https://api.example.com/posts',
    'https://api.example.com/comments',
  ]

  const results = await Promise.all(urls.map((url) => fetch(url)))

  results.forEach((res) => {
    expect(res.ok).toBe(true)
  })
})
```

## 8. 并发控制

限制并发数量，避免资源耗尽：

```typescript
async function parallelLimit<T>(
  tasks: (() => Promise<T>)[],
  limit: number,
): Promise<T[]> {
  const results: T[] = []
  const executing = new Set<Promise<void>>()

  for (const task of tasks) {
    const p = task().then((result) => {
      results.push(result)
    })
    executing.add(p)
    p.then(() => executing.delete(p))

    if (executing.size >= limit) {
      await Promise.race(executing)
    }
  }

  await Promise.all(executing)
  return results
}

test('limited parallel', async () => {
  const tasks = Array.from({ length: 10 }, (_, i) => () => fetchData(i))
  const results = await parallelLimit(tasks, 3)
  expect(results).toHaveLength(10)
})
```

## 9. 测试中的竞态条件

```typescript
// 可能有竞态条件
test('race condition', async () => {
  let value = 0

  const increment = async () => {
    const current = value
    await delay(10)
    value = current + 1
  }

  await Promise.all([increment(), increment(), increment()])

  // 结果可能不是 3，因为存在竞态条件
  expect(value).toBeLessThanOrEqual(3)
})
```
