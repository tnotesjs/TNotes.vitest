# [0037. 全局变量 Mock](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0037.%20%E5%85%A8%E5%B1%80%E5%8F%98%E9%87%8F%20Mock)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vi.stubGlobal](#3-vistubglobal)
- [4. Mock fetch](#4-mock-fetch)
- [5. Mock console](#5-mock-console)
- [6. Mock window 对象](#6-mock-window-对象)
- [7. vi.unstubAllGlobals](#7-viunstuballglobals)
- [8. 在 beforeEach 中使用](#8-在-beforeeach-中使用)
- [9. Mock 定时器](#9-mock-定时器)
- [10. Mock URL](#10-mock-url)

<!-- endregion:toc -->

## 1. 本节内容

- vi.stubGlobal 替换全局变量
- Mock fetch
- Mock console
- Mock window 对象
- vi.unstubAllGlobals 恢复
- Mock 定时器

## 2. 评价

vi.stubGlobal 用于替换全局变量，适合 Mock fetch、console 等浏览器或 Node.js 全局 API。测试后记得 unstubAllGlobals 恢复。

## 3. vi.stubGlobal

`vi.stubGlobal` 替换全局变量：

```typescript
import { vi, test, expect, afterEach } from 'vitest'

afterEach(() => {
  vi.unstubAllGlobals()
})

test('stub global', () => {
  vi.stubGlobal(
    'fetch',
    vi.fn(() => Promise.resolve({ ok: true })),
  )

  expect(globalThis.fetch).toBeDefined()
  expect(vi.isMockFunction(globalThis.fetch)).toBe(true)
})
```

## 4. Mock fetch

```typescript
test('mock fetch', async () => {
  const mockFetch = vi.fn().mockResolvedValue({
    ok: true,
    json: () => Promise.resolve({ name: 'Alice' }),
  })

  vi.stubGlobal('fetch', mockFetch)

  const response = await fetch('/api/user')
  const data = await response.json()

  expect(mockFetch).toHaveBeenCalledWith('/api/user')
  expect(data).toEqual({ name: 'Alice' })
})
```

## 5. Mock console

```typescript
test('mock console.log', () => {
  const spy = vi.spyOn(console, 'log').mockImplementation(() => {})

  console.log('hello')

  expect(spy).toHaveBeenCalledWith('hello')

  spy.mockRestore()
})
```

## 6. Mock window 对象

在 jsdom 环境中 mock window 属性：

```typescript
test('mock window.location', () => {
  vi.stubGlobal('location', {
    ...window.location,
    href: 'https://example.com',
  })

  expect(window.location.href).toBe('https://example.com')
})
```

## 7. vi.unstubAllGlobals

`vi.unstubAllGlobals` 恢复所有被 stub 的全局变量：

```typescript
test('unstub globals', () => {
  vi.stubGlobal('fetch', vi.fn())
  expect(vi.isMockFunction(globalThis.fetch)).toBe(true)

  vi.unstubAllGlobals()
  // fetch 恢复到原始实现
})
```

## 8. 在 beforeEach 中使用

```typescript
describe('global stubs', () => {
  afterEach(() => {
    vi.unstubAllGlobals()
  })

  test('test 1', () => {
    vi.stubGlobal('fetch', vi.fn().mockResolvedValue({ ok: true }))
    // ...
  })

  test('test 2', () => {
    // fetch 已恢复，可以重新 stub
    vi.stubGlobal('fetch', vi.fn().mockRejectedValue(new Error('fail')))
  })
})
```

## 9. Mock 定时器

```typescript
test('mock timers via global', () => {
  vi.stubGlobal(
    'setTimeout',
    vi.fn((cb) => cb()),
  )

  const fn = vi.fn()
  setTimeout(fn, 1000)

  expect(fn).toHaveBeenCalled()
})
```

推荐使用 `vi.useFakeTimers()` 替代直接 mock 定时器。

## 10. Mock URL

```typescript
test('mock URL', () => {
  vi.stubGlobal(
    'URL',
    class MockURL {
      constructor(public href: string) {}
      toString() {
        return this.href
      }
    },
  )

  const url = new URL('https://example.com')
  expect(url.href).toBe('https://example.com')
})
```
