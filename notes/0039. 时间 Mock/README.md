# [0039. 时间 Mock](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0039.%20%E6%97%B6%E9%97%B4%20Mock)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.useFakeTimers](#3-viusefaketimers)
- [4. vi.advanceTimersByTime](#4-viadvancetimersbytime)
- [5. vi.runAllTimers](#5-virunalltimers)
- [6. vi.setSystemTime](#6-visetsystemtime)
- [7. setInterval 测试](#7-setinterval-测试)
- [8. beforeEach 中使用](#8-beforeeach-中使用)
- [9. Date.now 测试](#9-datenow-测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- vi.useFakeTimers 控制时间
- vi.advanceTimersByTime 前进时间
- vi.runAllTimers 执行定时器
- vi.setSystemTime 设置系统时间
- Date.now 测试

## 2. 评价

时间 Mock 与定时器测试（0027）功能类似但侧重点不同。setSystemTime 影响 Date.now 和 new Date()，advanceTimersByTime 影响定时器回调。

## 3. vi.useFakeTimers

`vi.useFakeTimers()` 替换全局定时器函数，使时间可控：

```typescript
import { vi, test, expect } from 'vitest'

test('fake timers', () => {
  vi.useFakeTimers()

  const fn = vi.fn()
  setTimeout(fn, 1000)

  expect(fn).not.toHaveBeenCalled()

  vi.advanceTimersByTime(1000)
  expect(fn).toHaveBeenCalledOnce()

  vi.useRealTimers()
})
```

## 4. vi.advanceTimersByTime

将时间前进指定毫秒：

```typescript
test('advance time', () => {
  vi.useFakeTimers()

  const results: number[] = []
  setTimeout(() => results.push(1), 100)
  setTimeout(() => results.push(2), 200)

  vi.advanceTimersByTime(150)
  expect(results).toEqual([1])

  vi.advanceTimersByTime(50)
  expect(results).toEqual([1, 2])

  vi.useRealTimers()
})
```

## 5. vi.runAllTimers

立即执行所有待执行的定时器：

```typescript
test('run all timers', () => {
  vi.useFakeTimers()

  const fn = vi.fn()
  setTimeout(fn, 100)
  setTimeout(fn, 5000)

  vi.runAllTimers()
  expect(fn).toHaveBeenCalledTimes(2)

  vi.useRealTimers()
})
```

## 6. vi.setSystemTime

设置当前系统时间，影响 `new Date()` 和 `Date.now()`：

```typescript
test('system time', () => {
  vi.useFakeTimers()
  vi.setSystemTime(new Date('2024-01-01'))

  expect(new Date().getFullYear()).toBe(2024)
  expect(new Date().getMonth()).toBe(0) // 0 = January

  vi.useRealTimers()
})
```

## 7. setInterval 测试

```typescript
test('setInterval', () => {
  vi.useFakeTimers()

  let count = 0
  const id = setInterval(() => count++, 500)

  vi.advanceTimersByTime(1500)
  expect(count).toBe(3)

  clearInterval(id)
  vi.useRealTimers()
})
```

## 8. beforeEach 中使用

```typescript
describe('timer tests', () => {
  beforeEach(() => {
    vi.useFakeTimers()
  })

  afterEach(() => {
    vi.useRealTimers()
  })

  test('test 1', () => {
    vi.setSystemTime(new Date('2024-01-01'))
    expect(new Date().getFullYear()).toBe(2024)
  })

  test('test 2', () => {
    vi.setSystemTime(new Date('2025-06-01'))
    expect(new Date().getFullYear()).toBe(2025)
  })
})
```

## 9. Date.now 测试

```typescript
test('Date.now', () => {
  vi.useFakeTimers()
  vi.setSystemTime(new Date('2024-01-01T00:00:00Z'))

  expect(Date.now()).toBe(new Date('2024-01-01T00:00:00Z').getTime())

  vi.advanceTimersByTime(1000)
  expect(Date.now()).toBe(new Date('2024-01-01T00:00:01Z').getTime())

  vi.useRealTimers()
})
```

## 10. 注意事项

- 使用 fake timers 后必须调用 `vi.useRealTimers()` 恢复
- `vi.advanceTimersByTime` 会触发 `Date.now()` 的变化
- 建议在 `beforeEach`/`afterEach` 中统一管理 fake timers
