# [0035. 动态导入 Mock](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0035.%20%E5%8A%A8%E6%80%81%E5%AF%BC%E5%85%A5%20Mock)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. ESM 模块 Mock](#3-esm-模块-mock)
- [4. hoisting 行为](#4-hoisting-行为)
- [5. 动态 import](#5-动态-import)
- [6. 动态 import 与 Mock](#6-动态-import-与-mock)
- [7. 条件 Mock](#7-条件-mock)
- [8. vi.importActual](#8-viimportactual)
- [9. Mock 动态导入](#9-mock-动态导入)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- ESM 模块 Mock
- hoisting 行为
- 动态 import 使用
- 动态 import 与 Mock 结合
- 条件 Mock
- vi.importActual 获取原始模块

## 2. 评价

Vitest 原生支持 ESM 模块的 Mock，动态 import 配合 vi.importActual 可以实现部分 Mock。注意 vi.mock 的 hoisting 限制。

## 3. ESM 模块 Mock

Vitest 原生支持 ESM 模块的 Mock，不需要额外配置：

```typescript
vi.mock('./module', () => ({
  someFunction: vi.fn(() => 'mocked'),
}))

import { someFunction } from './module'

test('ESM mock', () => {
  expect(someFunction()).toBe('mocked')
})
```

## 4. hoisting 行为

`vi.mock` 和 `vi.hoisted` 会被提升到文件顶部，在所有 import 之前执行：

```typescript
// vi.mock 实际执行顺序在 import 之前
vi.mock('./api', () => ({
  fetchUser: vi.fn(() => ({ id: 1 })),
}))

// 这里的 import 会拿到 mock 后的模块
import { fetchUser } from './api'

test('hoisting', () => {
  expect(fetchUser(1)).toEqual({ id: 1 })
})
```

## 5. 动态 import

使用动态 `import()` 在测试中按需加载模块：

```typescript
test('dynamic import', async () => {
  const { add } = await import('./math')
  expect(add(1, 2)).toBe(3)
})
```

## 6. 动态 import 与 Mock

```typescript
vi.mock('./math', () => ({
  add: vi.fn(() => 100),
  subtract: vi.fn(() => 0),
}))

test('dynamic import with mock', async () => {
  const math = await import('./math')
  expect(math.add(1, 2)).toBe(100)
})
```

## 7. 条件 Mock

```typescript
test('conditional mock', async () => {
  const condition = true

  if (condition) {
    vi.mock('./api', () => ({
      fetchData: vi.fn(() => 'mocked'),
    }))
  }

  const { fetchData } = await import('./api')
  expect(fetchData()).toBe('mocked')
})
```

## 8. vi.importActual

`vi.importActual` 获取原始模块（未被 mock 的版本）：

```typescript
vi.mock('./utils', async () => {
  const actual = await vi.importActual<typeof import('./utils')>('./utils')

  return {
    ...actual,
    formatDate: vi.fn(() => '2024-01-01'),
  }
})
```

## 9. Mock 动态导入

如果模块使用动态导入，可以 mock `import()` 本身：

```typescript
vi.mock('./lazy-module', () => ({
  default: vi.fn(() => 'lazy mock'),
}))

test('mock lazy module', async () => {
  const mod = await import('./lazy-module')
  expect(mod.default()).toBe('lazy mock')
})
```

## 10. 注意事项

- `vi.mock` 的 hoisting 行为意味着它不能访问测试作用域中的变量
- 工厂函数中的变量必须在 `vi.mock` 内部定义
- 如果需要访问外部变量，使用 `vi.hoisted`
