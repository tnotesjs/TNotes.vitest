# [0033. Mock 状态管理](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0033.%20Mock%20%E7%8A%B6%E6%80%81%E7%AE%A1%E7%90%86)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. mockClear](#3-mockclear)
- [4. mockReset](#4-mockreset)
- [5. mockRestore](#5-mockrestore)
- [6. 三者对比](#6-三者对比)
- [7. vi.restoreAllMocks](#7-virestoreallmocks)
- [8. 在 beforeEach 中使用](#8-在-beforeeach-中使用)
- [9. 配置中自动恢复](#9-配置中自动恢复)

<!-- endregion:toc -->

## 1. 本节内容

- mockClear 清除调用记录
- mockReset 重置实现
- mockRestore 恢复原始实现
- 三者对比
- vi.restoreAllMocks
- 配置中自动恢复

## 2. 评价

mockClear/mockReset/mockRestore 三者清除程度递增。推荐配置 restoreMocks: true 自动恢复，避免 Mock 泄漏影响其他测试。

## 3. mockClear

`mockClear` 清除 Mock 函数的调用记录，但保留实现：

```typescript
import { test, expect, vi } from 'vitest'

test('mockClear', () => {
  const fn = vi.fn(() => 42)

  fn('a')
  fn('b')
  expect(fn).toHaveBeenCalledTimes(2)

  fn.mockClear()

  // 调用记录已清除
  expect(fn).not.toHaveBeenCalled()

  // 实现仍然保留
  expect(fn()).toBe(42)
})
```

使用场景：在 `beforeEach` 中清除调用记录，避免测试间相互影响。

## 4. mockReset

`mockReset` 清除调用记录并重置实现，函数将返回 `undefined`：

```typescript
test('mockReset', () => {
  const fn = vi.fn(() => 42)

  fn()
  expect(fn()).toBe(42)

  fn.mockReset()

  expect(fn).not.toHaveBeenCalled()
  expect(fn()).toBeUndefined() // 实现被重置
})
```

`mockReset` 比 `mockClear` 更彻底，会清除所有 mock 配置。

## 5. mockRestore

`mockRestore` 恢复到原始实现（仅对 `vi.spyOn` 创建的 Spy 有效）：

```typescript
test('mockRestore', () => {
  const obj = {
    method: () => 'original',
  }

  const spy = vi.spyOn(obj, 'method').mockReturnValue('mocked')

  expect(obj.method()).toBe('mocked')

  spy.mockRestore()

  expect(obj.method()).toBe('original')
})
```

## 6. 三者对比

| 方法          | 清除调用记录 | 清除实现 | 恢复原始实现   |
| ------------- | ------------ | -------- | -------------- |
| `mockClear`   | 是           | 否       | 否             |
| `mockReset`   | 是           | 是       | 否             |
| `mockRestore` | 是           | 是       | 是（仅 spyOn） |

## 7. vi.restoreAllMocks

`vi.restoreAllMocks()` 恢复所有被 mock 的方法：

```typescript
afterEach(() => {
  vi.restoreAllMocks()
})

test('test 1', () => {
  const spy = vi.spyOn(console, 'log').mockImplementation(() => {})
  console.log('hello')
  expect(spy).toHaveBeenCalled()
})

test('test 2', () => {
  // console.log 已恢复，spy 不影响其他测试
  const spy = vi.spyOn(console, 'log').mockImplementation(() => {})
  console.log('world')
  expect(spy).toHaveBeenCalled()
})
```

## 8. 在 beforeEach 中使用

```typescript
describe('mock management', () => {
  const fn = vi.fn()

  beforeEach(() => {
    fn.mockClear() // 每个测试前清除调用记录
  })

  test('test 1', () => {
    fn('a')
    expect(fn).toHaveBeenCalledTimes(1)
  })

  test('test 2', () => {
    fn('b')
    expect(fn).toHaveBeenCalledTimes(1) // 不受 test 1 影响
  })
})
```

## 9. 配置中自动恢复

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    clearMocks: true, // 每个测试前自动 mockClear
    restoreMocks: true, // 每个测试后自动 mockRestore
    mockReset: true, // 每个测试前自动 mockReset
  },
})
```

配置 `restoreMocks: true` 是推荐做法，可以避免 mock 泄漏到其他测试。
