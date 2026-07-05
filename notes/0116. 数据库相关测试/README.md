# [0116. 数据库相关测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0116.%20%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9B%B8%E5%85%B3%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 内存数据库](#3-内存数据库)
- [4. 事务回滚](#4-事务回滚)
- [5. 测试数据种子](#5-测试数据种子)
- [6. Mock 数据库](#6-mock-数据库)
- [7. 容器化测试](#7-容器化测试)
- [8. 等待数据库就绪](#8-等待数据库就绪)
- [9. 测试隔离](#9-测试隔离)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 内存数据库（:memory:）
- 事务回滚
- 测试数据种子
- Mock 数据库
- 容器化测试（Docker）
- 等待数据库就绪
- 测试隔离

## 2. 评价

- 数据库测试推荐使用内存数据库或事务回滚保持数据干净。容器化测试提供真实的数据库环境，但配置较复杂。

## 3. 内存数据库

使用内存数据库避免影响真实数据：

```typescript
import Database from 'better-sqlite3'

describe('database', () => {
  let db: Database.Database

  beforeEach(() => {
    db = new Database(':memory:')
    db.exec('CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)')
  })

  afterEach(() => {
    db.close()
  })
})
```

## 4. 事务回滚

使用事务回滚保持数据库干净：

```typescript
describe('with transaction', () => {
  let db: Database

  beforeEach(async () => {
    db = await createDatabase()
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

## 5. 测试数据种子

```typescript
async function seedTestData(db: Database) {
  await db.query('INSERT INTO users (name) VALUES (?)', ['Alice'])
  await db.query('INSERT INTO users (name) VALUES (?)', ['Bob'])
  await db.query('INSERT INTO posts (title, user_id) VALUES (?, ?)', [
    'Post 1',
    1,
  ])
}

beforeEach(async () => {
  await seedTestData(db)
})
```

## 6. Mock 数据库

```typescript
vi.mock('./db', () => ({
  query: vi.fn(),
  insert: vi.fn(),
}))
```

## 7. 容器化测试

使用 Docker 运行测试数据库：

```yaml
# docker-compose.test.yml
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: test
      POSTGRES_PASSWORD: test
    ports:
      - '5432:5432'
```

## 8. 等待数据库就绪

```typescript
beforeAll(async () => {
  await waitForDatabase('localhost:5432')
})
```

## 9. 测试隔离

确保每个测试的数据库状态独立：

```typescript
afterEach(async () => {
  await db.query('DELETE FROM users')
  await db.query('DELETE FROM posts')
})
```

## 10. 注意事项

- 使用内存数据库提高速度
- 每个测试后清理数据
- 使用事务回滚而非 DELETE
- 容器化测试需要额外配置
