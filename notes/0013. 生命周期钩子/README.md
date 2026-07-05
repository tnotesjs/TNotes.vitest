# [0013. 生命周期钩子](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0013.%20%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 钩子概览](#3-钩子概览)
- [4. beforeAll](#4-beforeall)
- [5. beforeEach](#5-beforeeach)
- [6. afterEach](#6-aftereach)
- [7. afterAll](#7-afterall)
- [8. 执行顺序](#8-执行顺序)
- [9. 异步钩子](#9-异步钩子)

<!-- endregion:toc -->

## 1. 本节内容

- 四个生命周期钩子（beforeAll/beforeEach/afterEach/afterAll）
- 钩子执行顺序
- 异步钩子支持

## 2. 评价

生命周期钩子用于测试前的初始化和测试后的清理。beforeEach 适合重置状态，afterEach 适合清理资源，正确的执行顺序确保测试隔离。

## 3. 钩子概览

Vitest 提供四个生命周期钩子：

| 钩子         | 执行时机                         | 执行次数     |
| ------------ | -------------------------------- | ------------ |
| `beforeAll`  | 当前 `describe` 内所有测试开始前 | 一次         |
| `beforeEach` | 每个测试用例开始前               | 每个测试一次 |
| `afterEach`  | 每个测试用例结束后               | 每个测试一次 |
| `afterAll`   | 当前 `describe` 内所有测试结束后 | 一次         |

## 4. beforeAll

`beforeAll` 在当前 `describe` 内的所有测试执行前运行一次，适合做一次性初始化：

```typescript
import { describe, test, expect, beforeAll } from 'vitest'

describe('database', () => {
  let db: Database

  beforeAll(async () => {
    db = await createTestDatabase()
    await db.seed(testData)
  })

  test('query user', async () => {
    const user = await db.query('SELECT * FROM users WHERE id = 1')
    expect(user.name).toBe('Alice')
  })

  test('count users', async () => {
    const count = await db.count('users')
    expect(count).toBeGreaterThan(0)
  })
})
```

## 5. beforeEach

`beforeEach` 在每个测试用例执行前运行，适合重置状态：

```typescript
import { describe, test, expect, beforeEach } from 'vitest'

describe('counter', () => {
  let count: number

  beforeEach(() => {
    count = 0
  })

  test('starts at 0', () => {
    expect(count).toBe(0)
  })

  test('increments', () => {
    count++
    expect(count).toBe(1)
  })

  test('still starts at 0', () => {
    expect(count).toBe(0)
  })
})
```

每个测试执行后 `count` 都会被重置为 0，测试之间不会相互影响。

## 6. afterEach

`afterEach` 在每个测试用例执行后运行，适合清理资源：

```typescript
import { describe, test, expect, afterEach } from 'vitest'

describe('temp files', () => {
  let tempFile: string

  afterEach(async () => {
    await fs.unlink(tempFile).catch(() => {})
  })

  test('creates temp file', async () => {
    tempFile = await createTempFile()
    expect(await fs.exists(tempFile)).toBe(true)
  })
})
```

## 7. afterAll

`afterAll` 在当前 `describe` 内的所有测试执行后运行一次，适合做一次性清理：

```typescript
import { describe, test, expect, beforeAll, afterAll } from 'vitest'

describe('server', () => {
  let server: Server

  beforeAll(async () => {
    server = await startTestServer(3000)
  })

  afterAll(async () => {
    await server.close()
  })

  test('responds to health check', async () => {
    const res = await fetch('http://localhost:3000/health')
    expect(res.ok).toBe(true)
  })
})
```

## 8. 执行顺序

对于嵌套的 `describe`，钩子的执行顺序遵循由外到内、由内到外的规则：

```typescript
describe('outer', () => {
  beforeAll(() => console.log('outer beforeAll'))
  beforeEach(() => console.log('outer beforeEach'))
  afterEach(() => console.log('outer afterEach'))
  afterAll(() => console.log('outer afterAll'))

  describe('inner', () => {
    beforeAll(() => console.log('inner beforeAll'))
    beforeEach(() => console.log('inner beforeEach'))
    afterEach(() => console.log('inner afterEach'))
    afterAll(() => console.log('inner afterAll'))

    test('test 1', () => {})
  })
})
```

输出顺序：

```
outer beforeAll
inner beforeAll
outer beforeEach
inner beforeEach
  -> test 1 执行
inner afterEach
outer afterEach
inner afterAll
outer afterAll
```

## 9. 异步钩子

所有钩子都支持异步函数：

```typescript
beforeEach(async () => {
  await resetDatabase()
})

afterAll(async () => {
  await cleanup()
})
```

如果异步钩子超时，测试会失败。可以通过 `hookTimeout` 配置超时时间。
