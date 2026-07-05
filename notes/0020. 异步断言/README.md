# [0020. 异步断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0020.%20%E5%BC%82%E6%AD%A5%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. resolves / rejects](#3-resolves--rejects)
- [4. async/await](#4-asyncawait)
- [5. 返回 Promise](#5-返回-promise)
- [6. 异步超时](#6-异步超时)
- [7. 多个异步断言](#7-多个异步断言)
- [8. Promise.all 并行测试](#8-promiseall-并行测试)
- [9. 异步错误处理](#9-异步错误处理)
- [10. 异步测试中的 Mock](#10-异步测试中的-mock)

<!-- endregion:toc -->

## 1. 本节内容

- resolves/rejects 断言 Promise
- async/await 方式
- 返回 Promise
- 异步超时
- Promise.all 并行测试
- 异步错误处理

## 2. 评价

异步断言支持 Promise 和 async/await 两种写法。推荐使用 async/await 方式，代码更清晰灵活。异步测试需注意超时配置。

## 3. resolves / rejects

`expect(...).resolves` 断言 Promise 成功并检查返回值：

```typescript
import { test, expect } from 'vitest'

async function fetchData(): Promise<string> {
  return 'hello'
}

test('resolves', async () => {
  await expect(fetchData()).resolves.toBe('hello')
})
```

`expect(...).rejects` 断言 Promise 失败并检查错误：

```typescript
async function failWithError(): Promise<never> {
  throw new Error('something went wrong')
}

test('rejects', async () => {
  await expect(failWithError()).rejects.toThrow('something went wrong')
})
```

注意：必须使用 `await` 等待断言完成。

## 4. async/await

最直接的异步测试方式是使用 `async/await`：

```typescript
test('async/await', async () => {
  const result = await fetchData()
  expect(result).toBe('hello')
})

test('async error', async () => {
  try {
    await failWithError()
  } catch (error) {
    expect(error).toBeInstanceOf(Error)
    expect(error.message).toBe('something went wrong')
  }
})
```

`async/await` 方式更灵活，可以在测试中进行多步断言。

## 5. 返回 Promise

测试函数可以返回 Promise，Vitest 会等待其完成：

```typescript
test('return promise', () => {
  return fetchData().then((result) => {
    expect(result).toBe('hello')
  })
})
```

这是 Jest 兼容的写法，推荐使用 `async/await` 替代。

## 6. 异步超时

异步测试默认超时时间为 5 秒。可以通过第三个参数设置超时：

```typescript
test('slow async test', async () => {
  const result = await slowOperation()
  expect(result).toBeDefined()
}, 10000) // 10 秒超时
```

也可以在配置中全局设置：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    testTimeout: 10000,
  },
})
```

## 7. 多个异步断言

```typescript
test('multiple async assertions', async () => {
  const user = await fetchUser(1)
  expect(user.name).toBe('Alice')
  expect(user.age).toBeGreaterThan(0)

  const posts = await fetchPosts(user.id)
  expect(posts).toBeInstanceOf(Array)
  expect(posts.length).toBeGreaterThan(0)
})
```

## 8. Promise.all 并行测试

```typescript
test('parallel async', async () => {
  const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()])

  expect(users).toBeInstanceOf(Array)
  expect(posts).toBeInstanceOf(Array)
})
```

## 9. 异步错误处理

```typescript
test('async error with rejects', async () => {
  await expect(fetchUser(999)).rejects.toThrow('User not found')
})

test('async error with try/catch', async () => {
  try {
    await fetchUser(999)
    expect.fail('should have thrown')
  } catch (error) {
    expect(error).toBeInstanceOf(Error)
    expect((error as Error).message).toBe('User not found')
  }
})
```

推荐使用 `rejects` 方式，代码更简洁。

## 10. 异步测试中的 Mock

```typescript
test('async mock', async () => {
  const mockFetch = vi.fn().mockResolvedValue({
    ok: true,
    json: () => Promise.resolve({ name: 'Alice' }),
  })

  const result = await mockFetch('/api/user')
  expect(result.ok).toBe(true)

  const data = await result.json()
  expect(data.name).toBe('Alice')
})
```
