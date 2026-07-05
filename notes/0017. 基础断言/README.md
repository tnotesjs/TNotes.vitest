# [0017. 基础断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0017.%20%E5%9F%BA%E7%A1%80%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. toBe](#3-tobe)
- [4. toEqual](#4-toequal)
- [5. toStrictEqual](#5-tostrictequal)
- [6. toBeTruthy / toBeFalsy](#6-tobetruthy--tobefalsy)
- [7. toBeNull / toBeUndefined / toBeDefined](#7-tobenull--tobeundefined--tobedefined)
- [8. toBeNaN](#8-tobenan)
- [9. toBeInstanceOf](#9-tobeinstanceof)

<!-- endregion:toc -->

## 1. 本节内容

- toBe 严格相等
- toEqual 深度相等
- toStrictEqual 严格深度相等
- 真假值断言
- null/undefined 断言
- toBeNaN
- toBeInstanceOf

## 2. 评价

基础断言是编写测试的核心工具。toBe 用于原始值比较，toEqual 用于对象比较，toStrictEqual 比 toEqual 更严格地检查 undefined 属性和类型。

## 3. toBe

`toBe` 使用 `Object.is` 进行严格相等比较：

```typescript
import { test, expect } from 'vitest'

test('toBe strict equality', () => {
  expect(1 + 1).toBe(2)
  expect('hello').toBe('hello')
  expect(true).toBe(true)
  expect(null).toBe(null)
})
```

`toBe` 适合比较原始值（数字、字符串、布尔值、null、undefined）。对于对象和数组，应使用 `toEqual`。

## 4. toEqual

`toEqual` 进行深度相等比较，递归检查对象的所有属性：

```typescript
test('toEqual deep comparison', () => {
  expect({ a: 1, b: 2 }).toEqual({ a: 1, b: 2 })
  expect([1, 2, 3]).toEqual([1, 2, 3])
  expect({ nested: { value: 42 } }).toEqual({ nested: { value: 42 } })
})
```

`toEqual` 会忽略 `undefined` 属性和数组空位。如果需要严格检查，使用 `toStrictEqual`。

## 5. toStrictEqual

`toStrictEqual` 比 `toEqual` 更严格：

```typescript
test('toStrictEqual vs toEqual', () => {
  // toEqual 不区分 undefined 属性
  expect({ a: 1, b: undefined }).toEqual({ a: 1 })

  // toStrictEqual 会检查 undefined 属性
  expect({ a: 1, b: undefined }).not.toStrictEqual({ a: 1 })

  // toStrictEqual 检查对象类型
  class Box {
    constructor(public value: number) {}
  }
  expect(new Box(1)).toStrictEqual(new Box(1))
  expect(new Box(1)).not.toEqual({ value: 1 })
})
```

## 6. toBeTruthy / toBeFalsy

```typescript
test('truthy and falsy', () => {
  expect(true).toBeTruthy()
  expect(1).toBeTruthy()
  expect('hello').toBeTruthy()
  expect([]).toBeTruthy()

  expect(false).toBeFalsy()
  expect(0).toBeFalsy()
  expect('').toBeFalsy()
  expect(null).toBeFalsy()
  expect(undefined).toBeFalsy()
})
```

`toBeTruthy` 和 `toBeFalsy` 检查值在布尔上下文中的表现，与 JavaScript 的真值/假值规则一致。

## 7. toBeNull / toBeUndefined / toBeDefined

```typescript
test('null and undefined', () => {
  expect(null).toBeNull()
  expect(undefined).toBeUndefined()

  const obj = { a: 1 }
  expect(obj.a).toBeDefined()
  expect(obj.b).toBeUndefined()
})
```

## 8. toBeNaN

```typescript
test('NaN check', () => {
  expect(NaN).toBeNaN()
  expect(0 / 0).toBeNaN()
  expect(Number('abc')).toBeNaN()
  expect(1).not.toBeNaN()
})
```

注意：不能使用 `toBe(NaN)` 来检查 NaN，因为 `NaN !== NaN`。

## 9. toBeInstanceOf

```typescript
test('instanceof check', () => {
  expect(new Date()).toBeInstanceOf(Date)
  expect(new Error('fail')).toBeInstanceOf(Error)
  expect([]).toBeInstanceOf(Array)
  expect(() => {}).toBeInstanceOf(Function)
})
```
