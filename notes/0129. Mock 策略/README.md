# [0129. Mock 策略](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0129.%20Mock%20%E7%AD%96%E7%95%A5)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 少 Mock 内部实现](#3-少-mock-内部实现)
- [4. 多 Mock 边界依赖](#4-多-mock-边界依赖)
- [5. 何时 Mock](#5-何时-mock)
- [6. 何时不 Mock](#6-何时不-mock)
- [7. Mock 的风险](#7-mock-的风险)
- [8. 测试边界](#8-测试边界)
- [9. 集成测试](#9-集成测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 少 Mock 内部实现
- 多 Mock 边界依赖（外部 API/数据库）
- 何时 Mock
- 何时不 Mock（纯函数）
- Mock 的风险（隐藏集成问题）
- 测试边界

## 2. 评价

- Mock 策略的核心是只 Mock 必要的外部依赖，保留真实的业务逻辑
- 过度 Mock 会降低测试价值，隐藏真实的集成问题

## 3. 少 Mock 内部实现

```typescript
// 不好的做法 - Mock 内部实现
vi.mock('./utils', () => ({
  validateEmail: vi.fn(() => true),
}))

// 好的做法 - 测试真实行为
test('validates email', () => {
  expect(validateEmail('test@example.com')).toBe(true)
  expect(validateEmail('invalid')).toBe(false)
})
```

## 4. 多 Mock 边界依赖

```typescript
// 好的做法 - Mock 外部 API
vi.mock('./api', () => ({
  fetchUser: vi.fn(() => ({ id: 1, name: 'Alice' })),
}))
```

## 5. 何时 Mock

- 外部 API 调用
- 数据库操作
- 文件系统操作
- 网络请求
- 第三方服务

## 6. 何时不 Mock

- 纯函数
- 工具函数
- 业务逻辑
- 数据转换

## 7. Mock 的风险

- Mock 可能与真实实现不一致
- Mock 会隐藏真实的集成问题
- 过度 Mock 降低测试价值

## 8. 测试边界

```typescript
// Mock 边界
vi.mock('./database', () => ({
  query: vi.fn(),
}))

// 测试真实的业务逻辑
test('processes user data', async () => {
  vi.mocked(query).mockResolvedValue([{ id: 1 }])
  const result = await processUsers()
  expect(result).toHaveLength(1)
})
```

## 9. 集成测试

对于边界依赖，考虑使用集成测试：

```typescript
// 真实的数据库调用
test('database integration', async () => {
  const users = await db.query('SELECT * FROM users')
  expect(users).toBeDefined()
})
```

## 10. 注意事项

- 优先测试真实行为
- 只 Mock 必要的外部依赖
- 定期审查 Mock 的必要性
