# [0118. MSW](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0118.%20MSW)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装](#3-安装)
- [4. 基本用法](#4-基本用法)
- [5. 处理不同请求](#5-处理不同请求)
- [6. 错误模拟](#6-错误模拟)
- [7. 延迟模拟](#7-延迟模拟)
- [8. 条件响应](#8-条件响应)
- [9. 测试中覆盖](#9-测试中覆盖)
- [10. 优势](#10-优势)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 msw
- 基本用法（setupServer）
- 处理不同请求（GET/POST/DELETE）
- 错误模拟
- 延迟模拟
- 条件响应
- 测试中覆盖

## 2. 评价

- MSW 在网络层拦截请求，不需要修改源代码。支持浏览器和 Node.js，是 Mock HTTP API 的推荐方案。

## 3. 安装

```bash
pnpm add -D msw
```

## 4. 基本用法

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
```

## 5. 处理不同请求

```typescript
const server = setupServer(
  http.get('/api/users', () => {
    return HttpResponse.json([{ id: 1, name: 'Alice' }])
  }),

  http.post('/api/users', async ({ request }) => {
    const body = await request.json()
    return HttpResponse.json({ id: 2, ...body })
  }),

  http.delete('/api/users/:id', ({ params }) => {
    return HttpResponse.json({ deleted: params.id })
  }),
)
```

## 6. 错误模拟

```typescript
const server = setupServer(
  http.get('/api/error', () => {
    return new HttpResponse(null, { status: 500 })
  }),
)
```

## 7. 延迟模拟

```typescript
const server = setupServer(
  http.get('/api/slow', async () => {
    await new Promise((resolve) => setTimeout(resolve, 1000))
    return HttpResponse.json({ data: 'slow' })
  }),
)
```

## 8. 条件响应

```typescript
const server = setupServer(
  http.get('/api/user', ({ request }) => {
    const url = new URL(request.url)
    const id = url.searchParams.get('id')

    if (id === '1') {
      return HttpResponse.json({ id: 1, name: 'Alice' })
    }

    return new HttpResponse(null, { status: 404 })
  }),
)
```

## 9. 测试中覆盖

```typescript
test('custom response', async () => {
  server.use(
    http.get('/api/user', () => {
      return HttpResponse.json({ id: 1, name: 'Bob' })
    }),
  )

  const user = await fetchUser(1)
  expect(user.name).toBe('Bob')
})
```

## 10. 优势

- 真实的网络层拦截
- 不需要修改源代码
- 支持浏览器和 Node.js
- 类型安全
