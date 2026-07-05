# [0025. Promise 测试](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0025.%20Promise%20%E6%B5%8B%E8%AF%95)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 返回 Promise](#3-返回-promise)
- [4. async/await](#4-asyncawait)
- [5. 多步异步操作](#5-多步异步操作)
- [6. 并行异步操作](#6-并行异步操作)
- [7. 异步错误处理](#7-异步错误处理)
- [8. async matcher](#8-async-matcher)
- [9. 超时处理](#9-超时处理)

<!-- endregion:toc -->

## 1. 本节内容

- 返回 Promise 测试
- async/await 方式
- 多步异步操作
- 并行异步操作
- 异步错误处理
- async matcher
- 超时处理

## 2. 评价

Promise 测试推荐使用 async/await 方式，代码更清晰。并行操作使用 Promise.all 加速，注意设置合理的超时时间。

## 3. 返回 Promise

测试函数可以返回 Promise，Vitest 会等待其完成：

```typescript
import { test, expect } from 'vitest'

function fetchData(): Promise<string> {
  return new Promise((resolve) => {
    setTimeout(() => resolve('hello'), 100)
  })
}

test('returns promise', () => {
  return fetchData().then((result) => {
    expect(result).toBe('hello')
  })
})
```

如果忘记返回 Promise 或使用 await，测试可能在断言执行前就通过了：

```typescript
// 错误写法 - 断言不会被执行
test('wrong', () => {
  fetchData().then((result) => {
    expect(result).toBe('hello')
  })
})

// 正确写法 - 返回 Promise
test('correct', () => {
  return fetchData().then((result) => {
    expect(result).toBe('hello')
  })
})
```

## 4. async/await

推荐使用 `async/await`，代码更清晰：

```typescript
test('async/await', async () => {
  const result = await fetchData()
  expect(result).toBe('hello')
})
```

## 5. 多步异步操作

```typescript
test('multiple async steps', async () => {
  const user = await fetchUser(1)
  expect(user.name).toBe('Alice')

  const posts = await fetchPosts(user.id)
  expect(posts).toHaveLength(3)

  const firstPost = posts[0]
  expect(firstPost.title).toBeDefined()
})
```

## 6. 并行异步操作

```typescript
test('parallel async', async () => {
  const [users, posts] = await Promise.all([fetchUsers(), fetchPosts()])

  expect(users).toBeInstanceOf(Array)
  expect(posts).toBeInstanceOf(Array)
})
```

## 7. 异步错误处理

```typescript
test('async error', async () => {
  await expect(fetchUser(999)).rejects.toThrow('User not found')
})
```

## 8. async matcher

Vitest 的 matcher 本身也支持异步链式调用：

```typescript
test('async resolves', async () => {
  await expect(fetchData()).resolves.toBe('hello')
})

test('async rejects', async () => {
  await expect(fetchUser(999)).rejects.toThrow()
})
```

## 9. 超时处理

异步测试默认超时 5 秒，可以通过第三个参数调整：

```typescript
test('slow operation', async () => {
  const result = await slowFetch()
  expect(result).toBeDefined()
}, 10000) // 10 秒超时
```
