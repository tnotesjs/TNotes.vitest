# [0022. Mock 断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0022.%20Mock%20%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. toHaveBeenCalled](#3-tohavebeencalled)
- [4. toHaveBeenCalledTimes](#4-tohavebeencalledtimes)
- [5. toHaveBeenCalledWith](#5-tohavebeencalledwith)
- [6. toHaveBeenLastCalledWith](#6-tohavebeenlastcalledwith)
- [7. toHaveBeenNthCalledWith](#7-tohavebeennthcalledwith)
- [8. toHaveReturned](#8-tohavereturned)
- [9. toHaveReturnedWith](#9-tohavereturnedwith)
- [10. toHaveLastReturnedWith](#10-tohavelastreturnedwith)
- [11. 组合使用](#11-组合使用)

<!-- endregion:toc -->

## 1. 本节内容

- toHaveBeenCalled 断言被调用
- toHaveBeenCalledTimes 调用次数
- toHaveBeenCalledWith 调用参数
- toHaveReturned 返回值断言

## 2. 评价

Mock 断言用于验证函数的调用行为和返回值。组合使用调用次数和参数断言，可以精确验证 Mock 函数的交互情况。

## 3. toHaveBeenCalled

断言 Mock 函数被调用过：

```typescript
import { test, expect, vi } from 'vitest'

test('function was called', () => {
  const fn = vi.fn()
  fn()
  expect(fn).toHaveBeenCalled()
})

test('function was not called', () => {
  const fn = vi.fn()
  expect(fn).not.toHaveBeenCalled()
})
```

## 4. toHaveBeenCalledTimes

断言 Mock 函数被调用的次数：

```typescript
test('called exactly 3 times', () => {
  const fn = vi.fn()
  fn()
  fn()
  fn()
  expect(fn).toHaveBeenCalledTimes(3)
})
```

## 5. toHaveBeenCalledWith

断言 Mock 函数被调用时传入的参数：

```typescript
test('called with specific args', () => {
  const fn = vi.fn()
  fn('hello', 42)
  expect(fn).toHaveBeenCalledWith('hello', 42)
})
```

## 6. toHaveBeenLastCalledWith

断言 Mock 函数最后一次调用时的参数：

```typescript
test('last call args', () => {
  const fn = vi.fn()
  fn('first')
  fn('second')
  fn('third')
  expect(fn).toHaveBeenLastCalledWith('third')
})
```

## 7. toHaveBeenNthCalledWith

断言 Mock 函数第 N 次调用时的参数：

```typescript
test('nth call args', () => {
  const fn = vi.fn()
  fn('a')
  fn('b')
  fn('c')
  expect(fn).toHaveBeenNthCalledWith(1, 'a')
  expect(fn).toHaveBeenNthCalledWith(2, 'b')
  expect(fn).toHaveBeenNthCalledWith(3, 'c')
})
```

## 8. toHaveReturned

断言 Mock 函数至少返回过一次：

```typescript
test('function returned', () => {
  const fn = vi.fn(() => 42)
  fn()
  expect(fn).toHaveReturned()
})
```

## 9. toHaveReturnedWith

断言 Mock 函数返回了特定值：

```typescript
test('returned specific value', () => {
  const fn = vi.fn(() => 'hello')
  fn()
  expect(fn).toHaveReturnedWith('hello')
})
```

## 10. toHaveLastReturnedWith

断言 Mock 函数最后一次返回的值：

```typescript
test('last return value', () => {
  const fn = vi
    .fn()
    .mockReturnValueOnce('a')
    .mockReturnValueOnce('b')
    .mockReturnValue('c')

  fn()
  fn()
  fn()
  expect(fn).toHaveLastReturnedWith('c')
})
```

## 11. 组合使用

```typescript
test('combined mock assertions', () => {
  const fetchUser = vi.fn()
  fetchUser(1)
  fetchUser(2)

  expect(fetchUser).toHaveBeenCalledTimes(2)
  expect(fetchUser).toHaveBeenNthCalledWith(1, 1)
  expect(fetchUser).toHaveBeenNthCalledWith(2, 2)
  expect(fetchUser).toHaveBeenLastCalledWith(2)
})
```
