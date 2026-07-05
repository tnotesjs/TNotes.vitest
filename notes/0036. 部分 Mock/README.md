# [0036. 部分 Mock](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0036.%20%E9%83%A8%E5%88%86%20Mock)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.importActual](#3-viimportactual)
- [4. 保留原始实现](#4-保留原始实现)
- [5. Mock 单个函数](#5-mock-单个函数)
- [6. 配合 vi.spyOn](#6-配合-vispyon)
- [7. Mock 默认导出](#7-mock-默认导出)
- [8. 嵌套 Mock](#8-嵌套-mock)
- [9. 使用场景](#9-使用场景)

<!-- endregion:toc -->

## 1. 本节内容

- vi.importActual 获取原始模块
- 保留原始实现
- Mock 单个函数
- 配合 vi.spyOn
- Mock 默认导出
- 嵌套 Mock

## 2. 评价

部分 Mock 允许只 Mock 模块中的某些函数，保留其他函数的真实实现。通过 vi.importActual 获取原始模块后展开，实现精确控制。

## 3. vi.importActual

`vi.importActual` 获取未被 mock 的原始模块，用于部分 Mock：

```typescript
import { vi, test, expect } from 'vitest'

vi.mock('./utils', async () => {
  const actual = await vi.importActual<typeof import('./utils')>('./utils')

  return {
    ...actual,
    formatDate: vi.fn(() => '2024-01-01'),
  }
})

import { formatDate, capitalize } from './utils'

test('partial mock', () => {
  // formatDate 被 mock
  expect(formatDate(new Date())).toBe('2024-01-01')

  // capitalize 保持原始实现
  expect(capitalize('hello')).toBe('Hello')
})
```

## 4. 保留原始实现

```typescript
vi.mock('./database', async () => {
  const actual =
    await vi.importActual<typeof import('./database')>('./database')

  return {
    ...actual,
    connect: vi.fn(), // mock connect
    // query、close 等保持原始实现
  }
})
```

## 5. Mock 单个函数

```typescript
vi.mock('./api', async () => {
  const actual = await vi.importActual<typeof import('./api')>('./api')

  return {
    ...actual,
    fetchUser: vi.fn(() => ({ id: 1, name: 'Mock' })),
  }
})
```

## 6. 配合 vi.spyOn

另一种部分 Mock 方式是使用 `vi.spyOn`：

```typescript
import * as utils from './utils'

test('spyOn partial mock', () => {
  const spy = vi.spyOn(utils, 'formatDate').mockReturnValue('2024-01-01')

  expect(utils.formatDate(new Date())).toBe('2024-01-01')
  expect(utils.capitalize('hello')).toBe('Hello') // 原始实现

  spy.mockRestore()
})
```

## 7. Mock 默认导出

```typescript
vi.mock('./config', async () => {
  const actual = await vi.importActual<typeof import('./config')>('./config')

  return {
    ...actual,
    default: {
      ...actual.default,
      apiUrl: 'http://mock-api.com',
    },
  }
})
```

## 8. 嵌套 Mock

```typescript
vi.mock('./service', async () => {
  const actual = await vi.importActual<typeof import('./service')>('./service')

  return {
    ...actual,
    UserService: class MockUserService {
      getUser() {
        return { id: 1, name: 'Mock' }
      }
    },
  }
})
```

## 9. 使用场景

部分 Mock 适用于：

- 只想 mock 某个函数（如 API 调用），保留其他工具函数
- Mock 外部依赖，保留内部逻辑
- Mock 某个方法使其返回固定值，其他方法保持真实行为
