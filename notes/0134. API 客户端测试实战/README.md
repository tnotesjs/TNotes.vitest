# [0134. API 客户端测试实战](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0134.%20API%20%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%B5%8B%E8%AF%95%E5%AE%9E%E6%88%98)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Mock fetch](#3-mock-fetch)
- [4. 错误处理](#4-错误处理)
- [5. HTTP 错误](#5-http-错误)
- [6. 重试逻辑](#6-重试逻辑)
- [7. 请求参数](#7-请求参数)
- [8. MSW 集成](#8-msw-集成)
- [9. 超时测试](#9-超时测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Mock fetch
- 错误处理（网络错误）
- HTTP 错误（404/500）
- 重试逻辑
- 请求参数验证
- MSW 集成
- 超时测试

## 2. 评价

API 客户端测试需要 Mock 网络请求，测试成功和失败路径。重试和超时逻辑是常见的测试场景。

## 3. Mock fetch

```typescript
import { vi, test, expect } from 'vitest'

const mockFetch = vi.fn()
vi.stubGlobal('fetch', mockFetch)

test('fetches user', async () => {
  mockFetch.mockResolvedValue({
    ok: true,
    json: () => Promise.resolve({ id: 1, name: 'Alice' }),
  })

  const user = await fetchUser(1)
  expect(user.name).toBe('Alice')
  expect(mockFetch).toHaveBeenCalledWith('/api/users/1')
})
```

## 4. 错误处理

```typescript
test('handles fetch error', async () => {
  mockFetch.mockRejectedValue(new Error('Network error'))

  await expect(fetchUser(1)).rejects.toThrow('Network error')
})
```

## 5. HTTP 错误

```typescript
test('handles HTTP error', async () => {
  mockFetch.mockResolvedValue({
    ok: false,
    status: 404,
    statusText: 'Not Found',
  })

  await expect(fetchUser(999)).rejects.toThrow('Not Found')
})
```

## 6. 重试逻辑

```typescript
test('retries on failure', async () => {
  mockFetch
    .mockRejectedValueOnce(new Error('Timeout'))
    .mockRejectedValueOnce(new Error('Timeout'))
    .mockResolvedValueOnce({
      ok: true,
      json: () => Promise.resolve({ id: 1 }),
    })

  const user = await fetchUserWithRetry(1, 3)
  expect(user.id).toBe(1)
  expect(mockFetch).toHaveBeenCalledTimes(3)
})
```

## 7. 请求参数

```typescript
test('sends correct params', async () => {
  mockFetch.mockResolvedValue({
    ok: true,
    json: () => Promise.resolve({}),
  })

  await createUser({ name: 'Alice', email: 'alice@example.com' })

  expect(mockFetch).toHaveBeenCalledWith(
    '/api/users',
    expect.objectContaining({
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: 'Alice', email: 'alice@example.com' }),
    }),
  )
})
```

## 8. MSW 集成

```typescript
import { http, HttpResponse } from 'msw'
import { setupServer } from 'msw/node'

const server = setupServer(
  http.get('/api/users/:id', ({ params }) => {
    return HttpResponse.json({ id: params.id, name: 'Alice' })
  }),
)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

## 9. 超时测试

```typescript
test('handles timeout', async () => {
  mockFetch.mockImplementation(
    () =>
      new Promise((_, reject) =>
        setTimeout(() => reject(new Error('Timeout')), 100),
      ),
  )

  await expect(fetchUser(1)).rejects.toThrow('Timeout')
})
```

## 10. 注意事项

- Mock 外部 API 调用
- 测试成功和失败路径
- 测试重试和超时逻辑
