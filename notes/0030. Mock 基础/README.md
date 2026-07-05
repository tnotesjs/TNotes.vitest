# [0030. Mock 基础](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0030.%20Mock%20%E5%9F%BA%E7%A1%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.fn 基础](#3-vifn-基础)
- [4. 返回值](#4-返回值)
- [5. 实现替换](#5-实现替换)
- [6. 异步返回值](#6-异步返回值)
- [7. 访问调用信息](#7-访问调用信息)
- [8. 传入已有函数](#8-传入已有函数)
- [9. Mock 类型](#9-mock-类型)

<!-- endregion:toc -->

## 1. 本节内容

- vi.fn 创建 Mock 函数
- mockReturnValue 设置返回值
- mockImplementation 替换实现
- 异步返回值
- 访问调用信息
- 传入已有函数

## 2. 评价

vi.fn 是 Vitest Mock 系统的基础，可以记录调用参数、次数和返回值。配合 mockReturnValue 和 mockImplementation 灵活控制函数行为。

## 3. vi.fn 基础

`vi.fn()` 创建一个 Mock 函数，可以记录调用信息：

```typescript
import { test, expect, vi } from 'vitest'

test('vi.fn basic', () => {
  const fn = vi.fn()

  fn('hello', 42)

  expect(fn).toHaveBeenCalledOnce()
  expect(fn).toHaveBeenCalledWith('hello', 42)
})
```

## 4. 返回值

通过 `mockReturnValue` 设置返回值：

```typescript
test('mock return value', () => {
  const fn = vi.fn()
  fn.mockReturnValue(42)

  expect(fn()).toBe(42)
})
```

通过 `mockReturnValueOnce` 设置单次返回值：

```typescript
test('mock return value once', () => {
  const fn = vi
    .fn()
    .mockReturnValueOnce('first')
    .mockReturnValueOnce('second')
    .mockReturnValue('default')

  expect(fn()).toBe('first')
  expect(fn()).toBe('second')
  expect(fn()).toBe('default')
  expect(fn()).toBe('default')
})
```

## 5. 实现替换

通过 `mockImplementation` 替换函数实现：

```typescript
test('mock implementation', () => {
  const fn = vi.fn((a: number, b: number) => a + b)

  expect(fn(1, 2)).toBe(3)
  expect(fn).toHaveBeenCalledWith(1, 2)
})
```

通过 `mockImplementationOnce` 设置单次实现：

```typescript
test('mock implementation once', () => {
  const fn = vi
    .fn()
    .mockImplementationOnce(() => 'first')
    .mockImplementationOnce(() => 'second')
    .mockImplementation(() => 'default')

  expect(fn()).toBe('first')
  expect(fn()).toBe('second')
  expect(fn()).toBe('default')
})
```

## 6. 异步返回值

```typescript
test('async mock', async () => {
  const fn = vi.fn()
  fn.mockResolvedValue({ name: 'Alice' })

  const result = await fn()
  expect(result).toEqual({ name: 'Alice' })
})

test('async mock rejection', async () => {
  const fn = vi.fn()
  fn.mockRejectedValue(new Error('fail'))

  await expect(fn()).rejects.toThrow('fail')
})
```

## 7. 访问调用信息

Mock 函数记录了每次调用的详细信息：

```typescript
test('call information', () => {
  const fn = vi.fn()
  fn('first', 1)
  fn('second', 2)

  // 调用参数
  expect(fn.mock.calls).toEqual([
    ['first', 1],
    ['second', 2],
  ])

  // 返回值
  expect(fn.mock.results).toEqual([
    { type: 'return', value: undefined },
    { type: 'return', value: undefined },
  ])

  // 调用次数
  expect(fn.mock.calls.length).toBe(2)
})
```

## 8. 传入已有函数

```typescript
test('wrap existing function', () => {
  const original = (x: number) => x * 2
  const fn = vi.fn(original)

  expect(fn(5)).toBe(10)
  expect(fn).toHaveBeenCalledWith(5)
})
```

## 9. Mock 类型

`vi.fn()` 创建的 Mock 函数具有额外的属性：

```typescript
const fn = vi.fn()

// mock 属性
fn.mock.calls // 调用参数数组
fn.mock.results // 返回值数组
fn.mock.instances // this 上下文数组
fn.mock.lastCall // 最后一次调用的参数

// mock 方法
fn.mockReturnValue(42)
fn.mockImplementation(() => 42)
fn.mockClear() // 清除调用记录
fn.mockReset() // 清除并重置实现
fn.mockRestore() // 恢复原始实现（仅 spyOn）
```
