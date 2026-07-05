# [0031. Spy 基础](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0031.%20Spy%20%E5%9F%BA%E7%A1%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.spyOn 基础](#3-vispyon-基础)
- [4. 监听对象方法](#4-监听对象方法)
- [5. 替换实现](#5-替换实现)
- [6. 监听并修改返回值](#6-监听并修改返回值)
- [7. 链式调用](#7-链式调用)
- [8. 监听 getter](#8-监听-getter)
- [9. 恢复所有 Spy](#9-恢复所有-spy)

<!-- endregion:toc -->

## 1. 本节内容

- vi.spyOn 监听对象方法
- 保留原始实现
- 替换实现
- 修改返回值
- 链式调用
- 监听 getter
- 恢复所有 Spy

## 2. 评价

vi.spyOn 创建 Spy 监听方法调用，同时保留原始实现。适合验证对象方法的调用情况，而不改变其实际行为。测试后记得 mockRestore。

## 3. vi.spyOn 基础

`vi.spyOn` 创建一个 Spy，监听对象方法的调用，同时保留原始实现：

```typescript
import { test, expect, vi } from 'vitest'

test('spyOn basic', () => {
  const calculator = {
    add: (a: number, b: number) => a + b,
  }

  const spy = vi.spyOn(calculator, 'add')

  const result = calculator.add(1, 2)

  expect(result).toBe(3) // 原始实现仍然执行
  expect(spy).toHaveBeenCalledWith(1, 2)
  expect(spy).toHaveReturnedWith(3)
})
```

## 4. 监听对象方法

```typescript
test('spy on object method', () => {
  const user = {
    name: 'Alice',
    greet() {
      return `Hello, ${this.name}!`
    },
  }

  const spy = vi.spyOn(user, 'greet')

  user.greet()

  expect(spy).toHaveBeenCalledOnce()
  expect(spy).toHaveReturnedWith('Hello, Alice!')
})
```

## 5. 替换实现

`vi.spyOn` 返回的 Spy 可以替换方法实现：

```typescript
test('spy with implementation', () => {
  const api = {
    fetchData: () => 'real data',
  }

  const spy = vi.spyOn(api, 'fetchData').mockImplementation(() => 'mock data')

  expect(api.fetchData()).toBe('mock data')
  expect(spy).toHaveBeenCalled()

  // 恢复原始实现
  spy.mockRestore()
  expect(api.fetchData()).toBe('real data')
})
```

## 6. 监听并修改返回值

```typescript
test('spy with return value', () => {
  const db = {
    getUser: (id: number) => ({ id, name: 'Alice' }),
  }

  const spy = vi.spyOn(db, 'getUser').mockReturnValue({ id: 1, name: 'Mock' })

  expect(db.getUser(1)).toEqual({ id: 1, name: 'Mock' })
  expect(spy).toHaveBeenCalledWith(1)
})
```

## 7. 链式调用

```typescript
test('spy chain', () => {
  const math = {
    add: (a: number, b: number) => a + b,
  }

  vi.spyOn(math, 'add')
    .mockReturnValueOnce(100)
    .mockReturnValueOnce(200)
    .mockReturnValue(0)

  expect(math.add(1, 2)).toBe(100)
  expect(math.add(1, 2)).toBe(200)
  expect(math.add(1, 2)).toBe(0)
})
```

## 8. 监听 getter

```typescript
test('spy on getter', () => {
  const config = {
    get isDev() {
      return process.env.NODE_ENV === 'development'
    },
  }

  const spy = vi.spyOn(config, 'isDev', 'get').mockReturnValue(true)

  expect(config.isDev).toBe(true)
  expect(spy).toHaveBeenCalled()
})
```

## 9. 恢复所有 Spy

```typescript
afterEach(() => {
  vi.restoreAllMocks()
})
```

`vi.restoreAllMocks()` 恢复所有被 spy 的方法到原始实现。
