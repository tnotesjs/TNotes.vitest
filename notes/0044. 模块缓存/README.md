# [0044. 模块缓存](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0044.%20%E6%A8%A1%E5%9D%97%E7%BC%93%E5%AD%98)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 模块缓存机制](#3-模块缓存机制)
- [4. vi.resetModules](#4-viresetmodules)
- [5. 测试隔离](#5-测试隔离)
- [6. 解决方案](#6-解决方案)
  - [6.1. 使用 vi.resetModules](#61-使用-viresetmodules)
  - [6.2. 使用 vi.mock](#62-使用-vimock)
  - [6.3. 配置自动重置](#63-配置自动重置)
- [7. 动态 import 与缓存](#7-动态-import-与缓存)
- [8. 模块缓存与 Mock](#8-模块缓存与-mock)
- [9. 注意事项](#9-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 模块缓存机制
- vi.resetModules 清除缓存
- 测试隔离
- 动态 import 与缓存
- 模块缓存与 Mock 的关系

## 2. 评价

模块缓存可能导致测试间相互影响。vi.resetModules 清除缓存后，下次 import 会重新加载模块，确保测试独立性。

## 3. 模块缓存机制

Vitest 默认会缓存已加载的模块。同一测试文件中多次 `import` 同一个模块，会返回相同的实例：

```typescript
import { myModule } from './module'

test('module is cached', () => {
  const a = myModule
  const b = myModule
  expect(a).toBe(b) // 同一个引用
})
```

## 4. vi.resetModules

`vi.resetModules` 清除模块缓存，下次 `import` 会重新加载模块：

```typescript
import { vi, test, expect } from 'vitest'

test('reset modules', async () => {
  const mod1 = await import('./module')

  vi.resetModules()

  const mod2 = await import('./module')

  expect(mod1).not.toBe(mod2) // 不同的引用
})
```

## 5. 测试隔离

模块缓存可能导致测试之间相互影响。如果测试 A 修改了模块的全局状态，测试 B 会看到修改后的状态：

```typescript
// module.ts
let count = 0
export function increment() {
  return ++count
}
export function getCount() {
  return count
}
```

```typescript
test('test A', async () => {
  const { increment, getCount } = await import('./module')
  increment()
  expect(getCount()).toBe(1)
})

test('test B', async () => {
  const { getCount } = await import('./module')
  expect(getCount()).toBe(1) // 受 test A 影响
})
```

## 6. 解决方案

### 6.1. 使用 vi.resetModules

```typescript
afterEach(() => {
  vi.resetModules()
})
```

### 6.2. 使用 vi.mock

```typescript
vi.mock('./module', () => ({
  increment: vi.fn(),
  getCount: vi.fn(() => 0),
}))
```

### 6.3. 配置自动重置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    unstubModules: true, // 每个测试后自动重置模块
  },
})
```

## 7. 动态 import 与缓存

使用动态 `import()` 配合 `vi.resetModules` 可以获取新的模块实例：

```typescript
test('fresh module', async () => {
  vi.resetModules()
  const mod = await import('./module')
  expect(mod.getCount()).toBe(0)
})
```

## 8. 模块缓存与 Mock

`vi.mock` 会影响模块缓存。mock 后的模块会替换缓存中的原始模块：

```typescript
vi.mock('./api', () => ({
  fetchUser: vi.fn(() => ({ id: 1, name: 'Mock' })),
}))

import { fetchUser } from './api'

test('mocked module', () => {
  expect(fetchUser(1)).toEqual({ id: 1, name: 'Mock' })
})
```

## 9. 注意事项

- 模块缓存是全局的，所有测试文件共享同一份缓存
- `vi.resetModules` 只清除 Vitest 管理的缓存，不清除 Node.js 原生缓存
- 在并发测试中，模块缓存可能导致不确定的行为
