# [0108. expectTypeOf](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0108.%20expectTypeOf)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 基本用法](#3-基本用法)
- [4. 对象类型](#4-对象类型)
- [5. 函数类型](#5-函数类型)
- [6. 泛型](#6-泛型)
- [7. 联合类型](#7-联合类型)
- [8. 字面量类型](#8-字面量类型)
- [9. 集合类型](#9-集合类型)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 基本用法
- 对象类型断言
- 函数类型断言
- 泛型支持
- 联合类型
- 字面量类型
- 集合类型

## 2. 评价

expectTypeOf 提供了丰富的类型断言方法，支持对象、函数、泛型等各种类型场景。是 Vitest 类型测试的核心 API。

## 3. 基本用法

`expectTypeOf` 用于类型断言：

```typescript
import { expectTypeOf } from 'vitest'

function add(a: number, b: number): number {
  return a + b
}

// 验证返回类型
expectTypeOf(add).returns.toBeNumber()

// 验证参数类型
expectTypeOf(add).parameter(0).toBeNumber()
expectTypeOf(add).parameter(1).toBeNumber()
```

## 4. 对象类型

```typescript
interface User {
  name: string
  age: number
  email?: string
}

function getUser(): User {
  return { name: 'Alice', age: 25 }
}

expectTypeOf(getUser).returns.toMatchType<{ name: string }>()
expectTypeOf(getUser).returns.toHaveProperty('name')
expectTypeOf(getUser).returns.name.toBeString()
```

## 5. 函数类型

```typescript
// 验证函数签名
expectTypeOf(add).toBeFunction()
expectTypeOf(add).parameters.toEqualTypeOf<[number, number]>()
expectTypeOf(add).returns.toBeNumber()
```

## 6. 泛型

```typescript
function identity<T>(value: T): T {
  return value
}

expectTypeOf(identity<number>).toBeFunction()
expectTypeOf(identity<number>)
  .parameter(0)
  .toBeNumber()
expectTypeOf(identity<number>).returns.toBeNumber()
```

## 7. 联合类型

```typescript
type Result = string | number

function process(input: Result): Result {
  return input
}

expectTypeOf(process).returns.toBeString()
expectTypeOf(process).returns.toBeNumber()
```

## 8. 字面量类型

```typescript
function getStatus(): 'success' | 'error' {
  return 'success'
}

expectTypeOf(getStatus).returns.toMatchTypeOf<'success'>()
expectTypeOf(getStatus).returns.toMatchTypeOf<'error'>()
```

## 9. 集合类型

```typescript
function getNumbers(): number[] {
  return [1, 2, 3]
}

expectTypeOf(getNumbers).returns.toEqualTypeOf<number[]>()
expectTypeOf(getNumbers).returns.items.toBeNumber()
```

## 10. 注意事项

- `expectTypeOf` 只检查类型，不运行代码
- 在 `.test-d.ts` 文件中使用
- 断言失败会在编译时报错
