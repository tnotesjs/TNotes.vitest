# [0034. 模块 Mock](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0034.%20%E6%A8%A1%E5%9D%97%20Mock)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.mock 基础](#3-vimock-基础)
- [4. hoisting 行为](#4-hoisting-行为)
- [5. 自动 Mock](#5-自动-mock)
- [6. 手动 Mock](#6-手动-mock)
- [7. Mock 文件](#7-mock-文件)
- [8. Mock 第三方模块](#8-mock-第三方模块)
- [9. 部分 Mock](#9-部分-mock)
- [10. 获取 Mock 实例](#10-获取-mock-实例)

<!-- endregion:toc -->

## 1. 本节内容

- vi.mock 替换整个模块
- hoisting 提升行为
- 自动 Mock
- 手动 Mock
- **mocks** 目录
- Mock 第三方模块
- 部分 Mock

## 2. 评价

vi.mock 是模块级 Mock 的核心 API，支持自动 Mock 和手动定义。hoisting 行为确保 Mock 在 import 之前生效。部分 Mock 通过 vi.importActual 实现。

## 3. vi.mock 基础

`vi.mock` 替换整个模块的导出：

```typescript
// api.ts
export function fetchUser(id: number) {
  return fetch(`/api/users/${id}`).then((r) => r.json())
}
```

```typescript
// api.test.ts
import { vi, test, expect } from 'vitest'

vi.mock('./api', () => ({
  fetchUser: vi.fn(() => ({ id: 1, name: 'Mock User' })),
}))

import { fetchUser } from './api'

test('mocked fetchUser', () => {
  const user = fetchUser(1)
  expect(user).toEqual({ id: 1, name: 'Mock User' })
})
```

## 4. hoisting 行为

`vi.mock` 会被提升到文件顶部，因此可以在 import 之前调用：

```typescript
// 以下写法是正确的，vi.mock 会自动提升
vi.mock('./api', () => ({
  fetchUser: vi.fn(),
}))

import { fetchUser } from './api'
```

## 5. 自动 Mock

`vi.mock` 不传工厂函数时，自动将所有导出替换为 `vi.fn()`：

```typescript
vi.mock('./api')

import { fetchUser } from './api'

test('auto mock', () => {
  // fetchUser 是一个自动创建的 vi.fn()
  expect(vi.isMockFunction(fetchUser)).toBe(true)
  expect(fetchUser()).toBeUndefined()
})
```

## 6. 手动 Mock

传入工厂函数手动定义 mock 的实现：

```typescript
vi.mock('./api', () => ({
  fetchUser: vi.fn(() => ({ id: 1, name: 'Manual Mock' })),
  fetchPosts: vi.fn(() => [{ id: 1, title: 'Post 1' }]),
}))
```

## 7. Mock 文件

将 mock 文件放在 `__mocks__` 目录下，Vitest 会自动加载：

```
src/
├── __mocks__/
│   └── api.ts        # mock 实现
├── api.ts            # 原始模块
└── api.test.ts
```

```typescript
// src/__mocks__/api.ts
export function fetchUser(id: number) {
  return { id, name: 'Mock User' }
}
```

```typescript
// src/api.test.ts
vi.mock('./api') // 自动加载 __mocks__/api.ts
```

## 8. Mock 第三方模块

```typescript
vi.mock('axios', () => ({
  default: {
    get: vi.fn(() => Promise.resolve({ data: {} })),
    post: vi.fn(() => Promise.resolve({ data: {} })),
  },
}))
```

## 9. 部分 Mock

使用 `vi.importActual` 保留部分原始实现：

```typescript
vi.mock('./utils', async () => {
  const actual = await vi.importActual<typeof import('./utils')>('./utils')
  return {
    ...actual,
    formatDate: vi.fn(() => '2024-01-01'),
  }
})
```

## 10. 获取 Mock 实例

```typescript
import { fetchUser } from './api'

vi.mock('./api')

test('mock instance', () => {
  const mockFn = vi.mocked(fetchUser)
  mockFn.mockReturnValue({ id: 1, name: 'Test' })

  expect(fetchUser(1)).toEqual({ id: 1, name: 'Test' })
})
```
