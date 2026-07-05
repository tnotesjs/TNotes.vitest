# [0027. 定时器测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0027.%20%E5%AE%9A%E6%97%B6%E5%99%A8%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.useFakeTimers](#3-viusefaketimers)
- [4. vi.advanceTimersByTime](#4-viadvancetimersbytime)
- [5. vi.runAllTimers](#5-virunalltimers)
- [6. vi.runOnlyPendingTimers](#6-virunonlypendingtimers)
- [7. setInterval 测试](#7-setinterval-测试)
- [8. vi.setSystemTime](#8-visetsystemtime)
- [9. beforeEach 中使用](#9-beforeeach-中使用)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- vi.useFakeTimers 控制时间
- vi.advanceTimersByTime 前进时间
- vi.runAllTimers 执行所有定时器
- setInterval 测试
- vi.setSystemTime 设置系统时间

## 2. 评价

Fake Timers 是测试定时器相关代码的核心工具，可以精确控制时间流逝而不需要实际等待。建议在 beforeEach/afterEach 中统一管理。

## 3. vi.useFakeTimers

`vi.useFakeTimers()` 替换全局定时器函数，使时间可以被控制：

```typescript
import { test, expect, vi } from 'vitest'

test('fake timers', () => {
  vi.useFakeTimers()

  const callback = vi.fn()
  setTimeout(callback, 1000)

  // 时间还没到，callback 不会被调用
  expect(callback).not.toHaveBeenCalled()

  // 前进 1000 毫秒
  vi.advanceTimersByTime(1000)

  expect(callback).toHaveBeenCalledOnce()

  vi.useRealTimers()
})
```

## 4. vi.advanceTimersByTime

`vi.advanceTimersByTime(ms)` 将时间前进指定毫秒：

```typescript
test('advance timers', () => {
  vi.useFakeTimers()

  const results: number[] = []

  setTimeout(() => results.push(1), 100)
  setTimeout(() => results.push(2), 200)
  setTimeout(() => results.push(3), 300)

  vi.advanceTimersByTime(150)
  expect(results).toEqual([1])

  vi.advanceTimersByTime(150)
  expect(results).toEqual([1, 2])

  vi.useRealTimers()
})
```

## 5. vi.runAllTimers

`vi.runAllTimers()` 立即执行所有待执行的定时器：

```typescript
test('run all timers', () => {
  vi.useFakeTimers()

  const fn = vi.fn()
  setTimeout(fn, 100)
  setTimeout(fn, 5000)
  setTimeout(fn, 10000)

  vi.runAllTimers()

  expect(fn).toHaveBeenCalledTimes(3)

  vi.useRealTimers()
})
```

## 6. vi.runOnlyPendingTimers

`vi.runOnlyPendingTimers()` 只执行当前排队的定时器，不执行定时器中新添加的定时器：

```typescript
test('run only pending', () => {
  vi.useFakeTimers()

  const fn = vi.fn(() => {
    setTimeout(fn, 100) // 递归添加
  })

  setTimeout(fn, 100)
  vi.runOnlyPendingTimers()

  expect(fn).toHaveBeenCalledTimes(1) // 只执行了第一次

  vi.useRealTimers()
})
```

## 7. setInterval 测试

```typescript
test('setInterval', () => {
  vi.useFakeTimers()

  let count = 0
  const id = setInterval(() => {
    count++
  }, 500)

  vi.advanceTimersByTime(1500)
  expect(count).toBe(3) // 500ms * 3 = 1500ms

  clearInterval(id)
  vi.useRealTimers()
})
```

## 8. vi.setSystemTime

`vi.setSystemTime()` 设置当前系统时间，影响 `new Date()` 和 `Date.now()`：

```typescript
test('system time', () => {
  vi.useFakeTimers()
  vi.setSystemTime(new Date('2024-01-01'))

  expect(new Date().getFullYear()).toBe(2024)
  expect(Date.now()).toBe(new Date('2024-01-01').getTime())

  vi.useRealTimers()
})
```

## 9. beforeEach 中使用

```typescript
describe('timer tests', () => {
  beforeEach(() => {
    vi.useFakeTimers()
  })

  afterEach(() => {
    vi.useRealTimers()
  })

  test('test 1', () => {
    const fn = vi.fn()
    setTimeout(fn, 100)
    vi.advanceTimersByTime(100)
    expect(fn).toHaveBeenCalled()
  })

  test('test 2', () => {
    vi.setSystemTime(new Date('2024-06-01'))
    expect(new Date().getMonth()).toBe(5)
  })
})
```

## 10. 注意事项

- 使用 fake timers 后必须调用 `vi.useRealTimers()` 恢复，否则影响后续测试
- `vi.advanceTimersByTime` 不会触发 `Date.now()` 的变化，需要 `vi.setSystemTime`
- fake timers 会影响 `setTimeout`、`setInterval`、`clearTimeout`、`clearInterval`
