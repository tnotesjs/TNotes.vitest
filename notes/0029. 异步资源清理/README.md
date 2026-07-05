# [0029. 异步资源清理](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0029.%20%E5%BC%82%E6%AD%A5%E8%B5%84%E6%BA%90%E6%B8%85%E7%90%86)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. afterAll 中清理](#3-afterall-中清理)
- [4. afterEach 中清理](#4-aftereach-中清理)
- [5. 异步清理失败](#5-异步清理失败)
- [6. 超时处理](#6-超时处理)
- [7. 服务器清理](#7-服务器清理)
- [8. 数据库事务回滚](#8-数据库事务回滚)
- [9. 临时目录清理](#9-临时目录清理)

<!-- endregion:toc -->

## 1. 本节内容

- afterAll 中清理资源
- afterEach 中清理
- 异步清理失败处理
- 超时处理
- 服务器清理
- 数据库事务回滚
- 临时目录清理

## 2. 评价

异步资源清理确保测试不会产生资源泄漏。afterEach 适合每个测试独立的资源，afterAll 适合共享资源。使用 try/catch 容忍清理失败。

## 3. afterAll 中清理

使用 `afterAll` 清理整个 `describe` 中创建的资源：

```typescript
import { describe, test, expect, beforeAll, afterAll } from 'vitest'

describe('database tests', () => {
  let db: Database

  beforeAll(async () => {
    db = await createTestDatabase()
    await db.connect()
  })

  afterAll(async () => {
    await db.disconnect()
  })

  test('query', async () => {
    const result = await db.query('SELECT 1')
    expect(result).toBeDefined()
  })
})
```

## 4. afterEach 中清理

每个测试后清理，避免测试间相互影响：

```typescript
describe('temp file tests', () => {
  let tempPath: string

  afterEach(async () => {
    if (tempPath) {
      await fs.unlink(tempPath).catch(() => {})
    }
  })

  test('creates file', async () => {
    tempPath = await createTempFile('test content')
    expect(await fs.exists(tempPath)).toBe(true)
  })
})
```

## 5. 异步清理失败

如果清理操作失败，测试会被标记为失败：

```typescript
afterAll(async () => {
  await server.close() // 如果 close 抛出异常，测试失败
})
```

可以使用 try/catch 容忍清理失败：

```typescript
afterAll(async () => {
  try {
    await server.close()
  } catch {
    // 容忍关闭失败，不影响测试结果
  }
})
```

## 6. 超时处理

异步清理默认跟随 `hookTimeout` 配置：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    hookTimeout: 10000, // 钩子超时 10 秒
  },
})
```

## 7. 服务器清理

```typescript
describe('HTTP server', () => {
  let server: Server

  beforeAll(async () => {
    server = await startServer(3000)
  })

  afterAll(async () => {
    await new Promise<void>((resolve, reject) => {
      server.close((err) => {
        if (err) reject(err)
        else resolve()
      })
    })
  })

  test('health check', async () => {
    const res = await fetch('http://localhost:3000/health')
    expect(res.ok).toBe(true)
  })
})
```

## 8. 数据库事务回滚

使用事务回滚代替清理数据：

```typescript
describe('with transaction', () => {
  let db: Database

  beforeAll(async () => {
    db = await createDatabase()
  })

  beforeEach(async () => {
    await db.beginTransaction()
  })

  afterEach(async () => {
    await db.rollback()
  })

  test('insert user', async () => {
    await db.query('INSERT INTO users (name) VALUES (?)', ['Alice'])
    const users = await db.query('SELECT * FROM users')
    expect(users).toHaveLength(1)
  })
})
```

每个测试结束后回滚事务，数据库保持干净。

## 9. 临时目录清理

```typescript
import { mkdtemp, rm } from 'node:fs/promises'
import { tmpdir } from 'node:os'
import { join } from 'node:path'

describe('temp directory', () => {
  let tempDir: string

  beforeEach(async () => {
    tempDir = await mkdtemp(join(tmpdir(), 'vitest-'))
  })

  afterEach(async () => {
    await rm(tempDir, { recursive: true, force: true })
  })

  test('write file', async () => {
    const filePath = join(tempDir, 'test.txt')
    await fs.writeFile(filePath, 'hello')
    const content = await fs.readFile(filePath, 'utf-8')
    expect(content).toBe('hello')
  })
})
```
