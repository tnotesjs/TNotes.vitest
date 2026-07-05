# [0109. assertType](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0109.%20assertType)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 基本用法](#3-基本用法)
- [4. 类型检查](#4-类型检查)
- [5. 函数参数](#5-函数参数)
- [6. 泛型](#6-泛型)
- [7. 与 expectTypeOf 的区别](#7-与-expecttypeof-的区别)
- [8. 使用场景](#8-使用场景)
- [9. 错误消息](#9-错误消息)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 基本用法
- 类型检查
- 函数参数类型
- 泛型支持
- 与 expectTypeOf 的区别
- 使用场景
- 错误消息

## 2. 评价

assertType 用于编译期类型检查，语法更简洁。与 expectTypeOf 的区别在于 assertType 只检查类型，不支持链式断言。

## 3. 基本用法

`assertType` 用于编译期类型检查：

```typescript
import { assertType } from 'vitest'

function add(a: number, b: number): number {
  return a + b
}

// 编译期检查，不会运行
assertType<number>(add(1, 2))
```

## 4. 类型检查

```typescript
interface User {
  name: string
  age: number
}

const user: User = { name: 'Alice', age: 25 }

// 正确的类型
assertType<User>(user)

// 错误的类型会编译失败
// assertType<string>(user) // Error
```

## 5. 函数参数

```typescript
function greet(name: string): string {
  return `Hello, ${name}`
}

assertType<string>(greet('Alice'))
```

## 6. 泛型

```typescript
function identity<T>(value: T): T {
  return value
}

assertType<number>(identity(42))
assertType<string>(identity('hello'))
```

## 7. 与 expectTypeOf 的区别

| 维度   | assertType             | expectTypeOf                    |
| ------ | ---------------------- | ------------------------------- |
| 语法   | `assertType<T>(value)` | `expectTypeOf(value).toBe<T>()` |
| 可读性 | 较低                   | 较高                            |
| 功能   | 基本类型检查           | 丰富的类型断言                  |

## 8. 使用场景

```typescript
// 验证库的 API 类型
assertType<Promise<Response>>(fetch('/api'))
assertType<number>(Math.random())
assertType<string>('hello'.toUpperCase())
```

## 9. 错误消息

当类型不匹配时，TypeScript 会报错：

```typescript
// Error: Argument of type 'number' is not assignable to parameter of type 'string'
assertType<string>(42)
```

## 10. 注意事项

- `assertType` 只在编译时检查
- 不会产生运行时代码
- 适合验证类型推断是否正确
