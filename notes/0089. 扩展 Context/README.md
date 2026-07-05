# [0089. 扩展 Context](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0089.%20%E6%89%A9%E5%B1%95%20Context)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 通过 beforeEach 注入](#3-通过-beforeeach-注入)
- [4. 动态数据](#4-动态数据)
- [5. 测试资源初始化](#5-测试资源初始化)
- [6. 工厂函数](#6-工厂函数)
- [7. 嵌套 Context](#7-嵌套-context)
- [8. 异步初始化](#8-异步初始化)
- [9. Context 的生命周期](#9-context-的生命周期)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- `beforeEach` 注入
- 动态数据
- 资源初始化
- 工厂函数
- 嵌套 Context
- 异步初始化

## 2. 评价

扩展 Context 是实现测试夹具的核心机制，通过 `beforeEach` 注入共享数据。

## 3. 通过 beforeEach 注入

```typescript
import { beforeEach, test, expect } from 'vitest'

beforeEach((context) => {
  context.greeting = 'hello'
  context.user = { name: 'Alice' }
})

test('injected data', ({ greeting, user }) => {
  expect(greeting).toBe('hello')
  expect(user.name).toBe('Alice')
})
```

## 4. 动态数据

```typescript
beforeEach((context) => {
  context.timestamp = Date.now()
  context.randomId = Math.random()
})

test('dynamic data', ({ timestamp, randomId }) => {
  expect(timestamp).toBeGreaterThan(0)
  expect(randomId).toBeGreaterThan(0)
})
```

## 5. 测试资源初始化

```typescript
beforeEach(async (context) => {
  context.db = await createTestDatabase()
  context.server = await startTestServer()
})

afterEach(async ({ db, server }) => {
  await db.close()
  await server.close()
})

test('database test', async ({ db }) => {
  const users = await db.query('SELECT * FROM users')
  expect(users).toBeDefined()
})
```

## 6. 工厂函数

```typescript
function createTestContext() {
  return {
    user: { id: 1, name: 'Alice' },
    token: 'test-token',
    api: createTestApi(),
  }
}

beforeEach((context) => {
  const testCtx = createTestContext()
  Object.assign(context, testCtx)
})
```

## 7. 嵌套 Context

```typescript
describe('suite', () => {
  beforeEach((context) => {
    context.outerData = 'outer'
  })

  describe('nested', () => {
    beforeEach((context) => {
      context.nestedData = 'nested'
    })

    test('both data', ({ outerData, nestedData }) => {
      expect(outerData).toBe('outer')
      expect(nestedData).toBe('nested')
    })
  })
})
```

## 8. 异步初始化

```typescript
beforeEach(async (context) => {
  context.data = await loadDataFromApi()
})
```

## 9. Context 的生命周期

- 每个测试开始前执行 `beforeEach`
- Context 在测试结束后自动清理
- 不同测试的 Context 相互独立

## 10. 注意事项

- Context 属性需要在 TypeScript 中声明类型
- 避免在 Context 中存储过大的数据
- 确保异步资源在 `afterEach` 中清理
