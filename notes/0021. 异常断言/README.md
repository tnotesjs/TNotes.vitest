# [0021. 异常断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0021.%20%E5%BC%82%E5%B8%B8%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. toThrow 基础](#3-tothrow-基础)
- [4. 检查错误消息](#4-检查错误消息)
- [5. 检查错误类型](#5-检查错误类型)
- [6. 异步异常](#6-异步异常)
- [7. 不抛出异常](#7-不抛出异常)
- [8. 自定义错误属性](#8-自定义错误属性)

<!-- endregion:toc -->

## 1. 本节内容

- toThrow 断言抛出异常
- 检查错误消息
- 检查错误类型
- 异步异常断言
- 不抛出异常断言
- 自定义错误属性

## 2. 评价

toThrow 是测试异常路径的核心断言，支持检查错误消息和错误类型。异步异常使用 rejects 方式断言。

## 3. toThrow 基础

`toThrow` 断言函数抛出异常：

```typescript
import { test, expect } from 'vitest'

function throwError() {
  throw new Error('something went wrong')
}

test('throws error', () => {
  expect(() => throwError()).toThrow()
})
```

注意：必须传入函数引用，不能直接传入函数调用结果。

```typescript
// 错误写法 - 函数会立即执行，toThrow 拿不到异常
expect(throwError()).toThrow()

// 正确写法 - 传入函数引用
expect(() => throwError()).toThrow()
```

## 4. 检查错误消息

```typescript
test('throws with message', () => {
  expect(() => throwError()).toThrow('something went wrong')
  expect(() => throwError()).toThrow(/went wrong/) // 支持正则
})
```

## 5. 检查错误类型

```typescript
test('throws specific error type', () => {
  expect(() => throwError()).toThrow(Error)

  class CustomError extends Error {
    code = 'CUSTOM'
  }

  expect(() => {
    throw new CustomError('custom error')
  }).toThrow(CustomError)
})
```

## 6. 异步异常

对于异步函数，使用 `rejects`：

```typescript
async function asyncThrow() {
  throw new Error('async error')
}

test('async throws', async () => {
  await expect(asyncThrow()).rejects.toThrow('async error')
})
```

## 7. 不抛出异常

使用 `not` 断言函数不抛出异常：

```typescript
function safeAdd(a: number, b: number) {
  return a + b
}

test('does not throw', () => {
  expect(() => safeAdd(1, 2)).not.toThrow()
})
```

## 8. 自定义错误属性

```typescript
class ApiError extends Error {
  constructor(
    message: string,
    public statusCode: number,
  ) {
    super(message)
  }
}

test('error with custom properties', () => {
  expect(() => {
    throw new ApiError('Not Found', 404)
  }).toThrow(ApiError)

  try {
    throw new ApiError('Not Found', 404)
  } catch (error) {
    expect((error as ApiError).statusCode).toBe(404)
  }
})
```
