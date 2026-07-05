# [0026. 回调式异步测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0026.%20%E5%9B%9E%E8%B0%83%E5%BC%8F%E5%BC%82%E6%AD%A5%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. done 回调](#3-done-回调)
- [4. 回调错误处理](#4-回调错误处理)
- [5. done 的超时](#5-done-的超时)
- [6. 推荐使用 async/await](#6-推荐使用-asyncawait)
- [7. Node.js 风格回调](#7-nodejs-风格回调)
- [8. 事件回调](#8-事件回调)
- [9. done 与 expect](#9-done-与-expect)

<!-- endregion:toc -->

## 1. 本节内容

- 回调方式
- 回调错误处理
- done 超时
- 推荐 async/await 替代
- Node.js 风格回调
- 事件回调

## 2. 评价

回调式异步测试通过 done 参数标记测试完成。现代代码推荐使用 async/await 替代回调方式，代码更简洁且错误处理更自然。

## 3. done 回调

Vitest 支持通过 `done` 回调测试基于回调的异步代码：

```typescript
import { test, expect } from 'vitest'

function fetchData(callback: (err: Error | null, data?: string) => void) {
  setTimeout(() => {
    callback(null, 'hello')
  }, 100)
}

test('callback style', (done) => {
  fetchData((err, data) => {
    expect(err).toBeNull()
    expect(data).toBe('hello')
    done()
  })
})
```

调用 `done()` 表示测试完成。如果 `done` 没有被调用，测试会超时失败。

## 4. 回调错误处理

```typescript
test('callback error', (done) => {
  fetchData((err, data) => {
    if (err) {
      done(err) // 传递错误给 done，测试立即失败
      return
    }
    expect(data).toBe('hello')
    done()
  })
})
```

## 5. done 的超时

`done` 回调默认超时 5 秒：

```typescript
test('slow callback', (done) => {
  setTimeout(() => {
    expect(true).toBe(true)
    done()
  }, 6000) // 会超时失败
}, 10000) // 设置 10 秒超时
```

## 6. 推荐使用 async/await

现代代码中，建议将回调式代码包装为 Promise，使用 `async/await` 测试：

```typescript
function fetchDataAsync(): Promise<string> {
  return new Promise((resolve, reject) => {
    fetchData((err, data) => {
      if (err) reject(err)
      else resolve(data!)
    })
  })
}

test('prefer async/await', async () => {
  const data = await fetchDataAsync()
  expect(data).toBe('hello')
})
```

## 7. Node.js 风格回调

Node.js 中常见的 `(err, result)` 回调模式：

```typescript
import { promisify } from 'node:util'
import { readFile } from 'node:fs'

const readFileAsync = promisify(readFile)

test('node callback with promisify', async () => {
  const content = await readFileAsync('package.json', 'utf-8')
  expect(content).toContain('vitest')
})
```

## 8. 事件回调

```typescript
import { EventEmitter } from 'node:events'

test('event emitter', (done) => {
  const emitter = new EventEmitter()

  emitter.on('data', (value) => {
    expect(value).toBe('hello')
    done()
  })

  setTimeout(() => emitter.emit('data', 'hello'), 50)
})
```

## 9. done 与 expect

```typescript
test('done with expect', (done) => {
  fetchData((err, data) => {
    expect(err).toBeNull()
    expect(data).toBeDefined()
    expect(typeof data).toBe('string')
    done()
  })
})
```

如果 `expect` 断言失败，Vitest 会捕获错误并标记测试失败，不需要手动调用 `done(err)`。
