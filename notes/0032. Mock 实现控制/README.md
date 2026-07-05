# [0032. Mock 实现控制](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0032.%20Mock%20%E5%AE%9E%E7%8E%B0%E6%8E%A7%E5%88%B6)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. mockImplementation](#3-mockimplementation)
- [4. mockImplementationOnce](#4-mockimplementationonce)
- [5. mockReturnValue](#5-mockreturnvalue)
- [6. mockReturnValueOnce](#6-mockreturnvalueonce)
- [7. mockResolvedValue](#7-mockresolvedvalue)
- [8. mockResolvedValueOnce](#8-mockresolvedvalueonce)
- [9. mockRejectedValue](#9-mockrejectedvalue)
- [10. 优先级](#10-优先级)

<!-- endregion:toc -->

## 1. 本节内容

- mockImplementation 替换完整实现
- mockImplementationOnce 单次替换
- mockReturnValue 固定返回值
- mockResolvedValue 异步返回
- mockRejectedValue 异步拒绝
- 优先级规则

## 2. 评价

Mock 实现控制提供了灵活的方式来定义 Mock 函数的行为。Once 系列方法只生效一次，适合模拟不同调用返回不同结果的场景。

## 3. mockImplementation

`mockImplementation` 替换 Mock 函数的完整实现：

```typescript
import { test, expect, vi } from 'vitest'

test('mockImplementation', () => {
  const fn = vi.fn()
  fn.mockImplementation((a: number, b: number) => a + b)

  expect(fn(1, 2)).toBe(3)
  expect(fn(10, 20)).toBe(30)
})
```

## 4. mockImplementationOnce

`mockImplementationOnce` 设置单次实现，调用后自动切换到下一个：

```typescript
test('mockImplementationOnce', () => {
  const fn = vi
    .fn()
    .mockImplementationOnce(() => 'first')
    .mockImplementationOnce(() => 'second')
    .mockImplementation(() => 'default')

  expect(fn()).toBe('first')
  expect(fn()).toBe('second')
  expect(fn()).toBe('default')
  expect(fn()).toBe('default')
})
```

## 5. mockReturnValue

`mockReturnValue` 设置固定的返回值：

```typescript
test('mockReturnValue', () => {
  const fn = vi.fn()
  fn.mockReturnValue(42)

  expect(fn()).toBe(42)
  expect(fn()).toBe(42) // 每次调用都返回 42
})
```

## 6. mockReturnValueOnce

`mockReturnValueOnce` 设置单次返回值：

```typescript
test('mockReturnValueOnce', () => {
  const fn = vi
    .fn()
    .mockReturnValueOnce('a')
    .mockReturnValueOnce('b')
    .mockReturnValue('c')

  expect(fn()).toBe('a')
  expect(fn()).toBe('b')
  expect(fn()).toBe('c')
  expect(fn()).toBe('c')
})
```

## 7. mockResolvedValue

`mockResolvedValue` 设置异步返回值（返回 resolved Promise）：

```typescript
test('mockResolvedValue', async () => {
  const fn = vi.fn()
  fn.mockResolvedValue({ id: 1, name: 'Alice' })

  const result = await fn()
  expect(result).toEqual({ id: 1, name: 'Alice' })
})
```

## 8. mockResolvedValueOnce

```typescript
test('mockResolvedValueOnce', async () => {
  const fn = vi
    .fn()
    .mockResolvedValueOnce('first')
    .mockResolvedValueOnce('second')
    .mockResolvedValue('default')

  expect(await fn()).toBe('first')
  expect(await fn()).toBe('second')
  expect(await fn()).toBe('default')
})
```

## 9. mockRejectedValue

`mockRejectedValue` 设置异步拒绝值（返回 rejected Promise）：

```typescript
test('mockRejectedValue', async () => {
  const fn = vi.fn()
  fn.mockRejectedValue(new Error('network error'))

  await expect(fn()).rejects.toThrow('network error')
})
```

## 10. 优先级

当同时设置多种 mock 时，优先级如下：

- `mockImplementationOnce` > `mockReturnValueOnce`（单次优先）
- `mockImplementation` > `mockReturnValue`（实现优先）
- 单次设置只生效一次，之后回到默认设置

```typescript
test('priority', () => {
  const fn = vi.fn()
  fn.mockReturnValue('default')
  fn.mockImplementation(() => 'implementation')

  // mockImplementation 优先级更高
  expect(fn()).toBe('implementation')
})
```
