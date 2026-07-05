# [0091. 适用场景](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0091.%20%E9%80%82%E7%94%A8%E5%9C%BA%E6%99%AF)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 测试夹具](#3-测试夹具)
- [4. 资源初始化](#4-资源初始化)
- [5. 依赖注入](#5-依赖注入)
- [6. 数据库测试](#6-数据库测试)
- [7. 浏览器测试](#7-浏览器测试)
- [8. Mock 服务](#8-mock-服务)
- [9. 临时文件](#9-临时文件)
- [10. 通用模式](#10-通用模式)

<!-- endregion:toc -->

## 1. 本节内容

- 测试夹具（Fixture）
- 资源初始化（数据库/服务器）
- 依赖注入
- 数据库测试
- 浏览器测试
- Mock 服务
- 临时文件

## 2. 评价

Test Context 非常适合创建测试夹具，通过 beforeEach 初始化资源，afterEach 清理，确保测试隔离。

## 3. 测试夹具

Test Context 非常适合创建测试夹具（Fixture）：

```typescript
beforeEach(async (context) => {
  context.db = await createTestDatabase()
  context.user = await context.db.createUser({ name: 'Alice' })
  context.token = generateToken(context.user)
})

afterEach(async ({ db }) => {
  await db.cleanup()
})

test('authenticated request', async ({ token }) => {
  const response = await fetch('/api/me', {
    headers: { Authorization: `Bearer ${token}` },
  })
  expect(response.ok).toBe(true)
})
```

## 4. 资源初始化

```typescript
beforeEach(async (context) => {
  context.server = await startTestServer(3000)
  context.client = createTestClient('http://localhost:3000')
})

afterEach(async ({ server }) => {
  await server.close()
})

test('API test', async ({ client }) => {
  const response = await client.get('/health')
  expect(response.status).toBe(200)
})
```

## 5. 依赖注入

```typescript
beforeEach((context) => {
  context.logger = createMockLogger()
  context.config = { debug: true, timeout: 5000 }
  context.api = createApi(context.config, context.logger)
})

test('dependency injection', ({ api, logger }) => {
  api.doSomething()
  expect(logger.info).toHaveBeenCalled()
})
```

## 6. 数据库测试

```typescript
describe('database', () => {
  beforeEach(async (context) => {
    context.db = await createTestDb()
    await context.db.migrate()
    await context.db.seed(testData)
  })

  afterEach(async ({ db }) => {
    await db.rollback()
    await db.close()
  })

  test('query users', async ({ db }) => {
    const users = await db.query('SELECT * FROM users')
    expect(users).toHaveLength(3)
  })
})
```

## 7. 浏览器测试

```typescript
describe('browser', () => {
  beforeEach(async (context) => {
    context.browser = await launchBrowser()
    context.page = await context.browser.newPage()
  })

  afterEach(async ({ browser }) => {
    await browser.close()
  })

  test('page title', async ({ page }) => {
    await page.goto('http://localhost:3000')
    expect(await page.title()).toBe('My App')
  })
})
```

## 8. Mock 服务

```typescript
beforeEach((context) => {
  context.mockApi = {
    fetchUser: vi.fn().mockResolvedValue({ id: 1, name: 'Alice' }),
    fetchPosts: vi.fn().mockResolvedValue([]),
  }
})

test('mock service', ({ mockApi }) => {
  expect(mockApi.fetchUser).toBeDefined()
})
```

## 9. 临时文件

```typescript
import { mkdtemp, rm } from 'node:fs/promises'
import { tmpdir } from 'node:os'
import { join } from 'node:path'

beforeEach(async (context) => {
  context.tempDir = await mkdtemp(join(tmpdir(), 'test-'))
})

afterEach(async ({ tempDir }) => {
  await rm(tempDir, { recursive: true, force: true })
})
```

## 10. 通用模式

```typescript
// 创建通用的测试夹具
function createFixture() {
  return {
    user: { id: 1, name: 'Alice' },
    token: 'test-token',
    timestamp: Date.now(),
  }
}

beforeEach((context) => {
  Object.assign(context, createFixture())
})
```
