# [0130. 可维护测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0130.%20%E5%8F%AF%E7%BB%B4%E6%8A%A4%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 减少脆弱断言](#3-减少脆弱断言)
- [4. 稳定的测试数据](#4-稳定的测试数据)
- [5. 清晰的 setup](#5-清晰的-setup)
- [6. 测试独立性](#6-测试独立性)
- [7. 避免测试实现细节](#7-避免测试实现细节)
- [8. 使用工厂函数](#8-使用工厂函数)
- [9. 测试辅助函数](#9-测试辅助函数)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 减少脆弱断言（避免依赖具体值）
- 稳定的测试数据（避免随机值）
- 清晰的 setup（beforeEach）
- 测试独立性
- 避免测试实现细节
- 使用工厂函数

## 2. 评价

- 可维护测试应该稳定、清晰、独立
- 避免脆弱断言，使用工厂函数创建测试数据，确保每个测试可以独立运行

## 3. 减少脆弱断言

```typescript
// 脆弱的断言
expect(result).toBe('Hello, Alice! Your order #12345 is confirmed.')

// 稳定的断言
expect(result).toContain('Alice')
expect(result).toContain('confirmed')
```

## 4. 稳定的测试数据

```typescript
// 不稳定
const user = { id: Date.now(), name: `User-${Math.random()}` }

// 稳定
const user = { id: 1, name: 'Alice' }
```

## 5. 清晰的 setup

```typescript
describe('User API', () => {
  let db: Database
  let server: Server

  beforeEach(async () => {
    db = await createTestDb()
    server = await startServer(db)
  })

  afterEach(async () => {
    await server.close()
    await db.close()
  })
})
```

## 6. 测试独立性

每个测试应该独立运行，不依赖其他测试。

## 7. 避免测试实现细节

```typescript
// 测试实现细节
test('calls setState', () => {
  const spy = vi.spyOn(component, 'setState')
  component.update()
  expect(spy).toHaveBeenCalled()
})

// 测试行为
test('displays updated count', async () => {
  await user.click(incrementButton)
  expect(screen.getByText('Count: 1')).toBeInTheDocument()
})
```

## 8. 使用工厂函数

```typescript
function createUser(overrides = {}) {
  return {
    id: 1,
    name: 'Alice',
    email: 'alice@example.com',
    ...overrides,
  }
}
```

## 9. 测试辅助函数

```typescript
// 提取公共逻辑
async function setupAuthenticatedUser() {
  const user = createUser()
  const token = generateToken(user)
  return { user, token }
}
```

## 10. 注意事项

- 测试应该是自文档化的
- 避免测试之间的依赖
- 定期重构测试代码
