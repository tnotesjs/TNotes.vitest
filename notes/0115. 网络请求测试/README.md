# [0115. 网络请求测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0115.%20%E7%BD%91%E7%BB%9C%E8%AF%B7%E6%B1%82%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Mock fetch](#3-mock-fetch)
- [4. Mock axios](#4-mock-axios)
- [5. MSW (Mock Service Worker)](#5-msw-mock-service-worker)
- [6. 错误模拟](#6-错误模拟)
- [7. 超时测试](#7-超时测试)
- [8. 请求参数验证](#8-请求参数验证)
- [9. 多次请求](#9-多次请求)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- Mock fetch
- Mock axios
- MSW（Mock Service Worker）
- 错误模拟
- 超时测试
- 请求参数验证
- 多次请求

## 2. 评价

- 网络请求测试推荐使用 MSW 拦截真实网络层，比直接 Mock fetch 更接近真实行为。支持浏览器和 Node.js 环境。

## 3. Mock fetch

```typescript
import { vi, test, expect } from 'vitest'

test('mock fetch', async () => {
  const mockFetch = vi.fn().mockResolvedValue({
    ok: true,
    json: () => Promise.resolve({ name: 'Alice' }),
  })

  vi.stubGlobal('fetch', mockFetch)

  const response = await fetch('/api/user')
  const data = await response.json()

  expect(mockFetch).toHaveBeenCalledWith('/api/user')
  expect(data).toEqual({ name: 'Alice' })
})
```

## 4. Mock axios

```typescript
vi.mock('axios', () => ({
  default: {
    get: vi.fn().mockResolvedValue({ data: { name: 'Alice' } }),
    post: vi.fn().mockResolvedValue({ data: { id: 1 } }),
  },
}))
```

## 5. MSW (Mock Service Worker)

```bash
pnpm add -D msw
```

```typescript
import { http, HttpResponse } from 'msw'
import { setupServer } from 'msw/node'

const server = setupServer(
  http.get('/api/user', () => {
    return HttpResponse.json({ name: 'Alice' })
  }),
)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

test('fetch user', async () => {
  const response = await fetch('/api/user')
  const data = await response.json()
  expect(data).toEqual({ name: 'Alice' })
})
```

## 6. 错误模拟

```typescript
test('fetch error', async () => {
  vi.stubGlobal('fetch', vi.fn().mockRejectedValue(new Error('Network error')))

  await expect(fetchData()).rejects.toThrow('Network error')
})
```

## 7. 超时测试

```typescript
test('timeout', async () => {
  vi.stubGlobal(
    'fetch',
    vi
      .fn()
      .mockImplementation(
        () =>
          new Promise((_, reject) =>
            setTimeout(() => reject(new Error('Timeout')), 100),
          ),
      ),
  )

  await expect(fetchData()).rejects.toThrow('Timeout')
})
```

## 8. 请求参数验证

```typescript
test('request params', async () => {
  const mockFetch = vi.fn().mockResolvedValue({
    ok: true,
    json: () => Promise.resolve({}),
  })

  vi.stubGlobal('fetch', mockFetch)

  await fetch('/api/user', {
    method: 'POST',
    body: JSON.stringify({ name: 'Alice' }),
  })

  expect(mockFetch).toHaveBeenCalledWith(
    '/api/user',
    expect.objectContaining({
      method: 'POST',
      body: '{"name":"Alice"}',
    }),
  )
})
```

## 9. 多次请求

```typescript
test('multiple requests', async () => {
  const mockFetch = vi
    .fn()
    .mockResolvedValueOnce({ ok: true, json: () => ({ name: 'Alice' }) })
    .mockResolvedValueOnce({ ok: true, json: () => ({ name: 'Bob' }) })

  vi.stubGlobal('fetch', mockFetch)

  const user1 = await fetchUser(1)
  const user2 = await fetchUser(2)

  expect(user1.name).toBe('Alice')
  expect(user2.name).toBe('Bob')
})
```

## 10. 注意事项

- 使用 `vi.stubGlobal` mock fetch
- 测试后恢复原始 fetch
- MSW 提供更真实的网络模拟
