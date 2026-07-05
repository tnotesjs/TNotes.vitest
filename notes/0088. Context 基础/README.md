# [0088. Context 基础](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0088.%20Context%20%E5%9F%BA%E7%A1%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 什么是 Test Context](#3-什么是-test-context)
- [4. 默认 Context](#4-默认-context)
- [5. 解构 Context](#5-解构-context)
- [6. task 对象](#6-task-对象)
- [7. 异步测试中的 Context](#7-异步测试中的-context)
- [8. Context 与生命周期钩子](#8-context-与生命周期钩子)
- [9. 注意事项](#9-注意事项)
- [10. 扩展 Context](#10-扩展-context)

<!-- endregion:toc -->

## 1. 本节内容

- Test Context 概念
- 默认 Context
- 解构 Context
- `task` 对象
- 异步测试
- 生命周期钩子

## 2. 评价

Test Context 提供测试函数的上下文信息，可以通过 `beforeEach` 注入自定义数据。

## 3. 什么是 Test Context

Test Context 是测试函数的上下文参数，可以在测试之间共享数据：

```typescript
import { test, expect } from 'vitest'

test('context', (context) => {
  // context 是测试的上下文对象
  expect(context).toBeDefined()
})
```

## 4. 默认 Context

默认情况下，Context 包含：

```typescript
test('default context', ({ expect, task }) => {
  // expect - 断言函数
  // task - 当前测试任务信息
  expect(task.name).toBe('default context')
})
```

## 5. 解构 Context

```typescript
test('destructure', ({ expect, task }) => {
  expect(task.suite.name).toBeDefined()
})
```

## 6. task 对象

`task` 包含当前测试的元信息：

```typescript
test('task info', ({ task }) => {
  console.log(task.name) // 测试名称
  console.log(task.suite) // 所属的 describe
  console.log(task.file) // 文件信息
  console.log(task.result) // 测试结果
})
```

## 7. 异步测试中的 Context

```typescript
test('async context', async ({ expect }) => {
  const result = await fetchData()
  expect(result).toBeDefined()
})
```

## 8. Context 与生命周期钩子

生命周期钩子也可以访问 Context：

```typescript
describe('suite', () => {
  beforeEach((context) => {
    // context 包含当前测试的信息
    console.log('Before:', context.task.name)
  })

  test('test', ({ expect }) => {
    expect(true).toBe(true)
  })
})
```

## 9. 注意事项

- Context 参数是可选的
- 不解构 Context 也可以直接使用参数
- Context 在每个测试中都是新的实例

## 10. 扩展 Context

可以通过 `beforeEach` 注入自定义数据到 Context：

```typescript
beforeEach((context) => {
  context.customData = 'hello'
})

test('custom data', ({ customData }) => {
  expect(customData).toBe('hello')
})
```
