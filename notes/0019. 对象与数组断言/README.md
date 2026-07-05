# [0019. 对象与数组断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0019.%20%E5%AF%B9%E8%B1%A1%E4%B8%8E%E6%95%B0%E7%BB%84%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. toHaveProperty](#3-tohaveproperty)
- [4. toContainEqual](#4-tocontainequal)
- [5. toMatchObject](#5-tomatchobject)
- [6. 对象深度比较](#6-对象深度比较)
- [7. toHaveBeenCalled（Mock 相关）](#7-tohavebeencalledmock-相关)
- [8. 数组长度与结构](#8-数组长度与结构)
- [9. expect.objectContaining](#9-expectobjectcontaining)

<!-- endregion:toc -->

## 1. 本节内容

- toHaveProperty 属性检查
- toContainEqual 深度包含
- toMatchObject 部分匹配
- expect.objectContaining
- expect.arrayContaining

## 2. 评价

对象和数组断言适合验证复杂数据结构。toMatchObject 只检查关键字段，适合验证 API 响应中的重要属性而忽略动态值。

## 3. toHaveProperty

检查对象是否包含某个属性：

```typescript
import { test, expect } from 'vitest'

test('has property', () => {
  const user = { name: 'Alice', age: 25 }

  expect(user).toHaveProperty('name')
  expect(user).toHaveProperty('age')
  expect(user).not.toHaveProperty('email')
})
```

可以同时检查属性值：

```typescript
test('has property with value', () => {
  const user = { name: 'Alice', address: { city: 'Beijing' } }

  expect(user).toHaveProperty('name', 'Alice')
  expect(user).toHaveProperty('address.city', 'Beijing') // 支持嵌套路径
})
```

## 4. toContainEqual

`toContainEqual` 在数组中查找深度相等的元素：

```typescript
test('array contains equal', () => {
  const users = [
    { name: 'Alice', age: 25 },
    { name: 'Bob', age: 30 },
  ]

  expect(users).toContainEqual({ name: 'Alice', age: 25 })
  expect(users).not.toContainEqual({ name: 'Charlie', age: 35 })
})
```

与 `toContain` 的区别：`toContain` 使用 `Object.is`，`toContainEqual` 使用深度相等比较。

## 5. toMatchObject

检查对象是否匹配部分结构：

```typescript
test('partial object match', () => {
  const response = {
    status: 200,
    data: {
      id: 1,
      name: 'Alice',
      email: 'alice@example.com',
      createdAt: '2024-01-01',
    },
  }

  // 只检查部分属性，忽略其他
  expect(response).toMatchObject({
    status: 200,
    data: { name: 'Alice' },
  })
})
```

`toMatchObject` 适合验证 API 响应中的关键字段，忽略时间戳、ID 等动态值。

## 6. 对象深度比较

```typescript
test('deep comparison', () => {
  const actual = {
    id: 1,
    items: [{ name: 'a' }, { name: 'b' }],
    meta: { count: 2 },
  }

  expect(actual).toEqual({
    id: 1,
    items: [{ name: 'a' }, { name: 'b' }],
    meta: { count: 2 },
  })
})
```

## 7. toHaveBeenCalled（Mock 相关）

检查 Mock 函数是否被调用：

```typescript
test('mock was called', () => {
  const fn = vi.fn()
  fn('hello')

  expect(fn).toHaveBeenCalled()
  expect(fn).toHaveBeenCalledTimes(1)
  expect(fn).toHaveBeenCalledWith('hello')
})
```

## 8. 数组长度与结构

```typescript
test('array structure', () => {
  const arr = [1, 2, 3, 4, 5]

  expect(arr).toHaveLength(5)
  expect(arr).toContain(3)
  expect(arr).toEqual(expect.arrayContaining([1, 3, 5]))
})
```

`expect.arrayContaining` 是一个非对称 matcher，检查数组是否包含指定元素（不要求完全相等）。

## 9. expect.objectContaining

```typescript
test('partial object', () => {
  const user = {
    id: 1,
    name: 'Alice',
    email: 'alice@example.com',
    createdAt: new Date(),
  }

  expect(user).toEqual(
    expect.objectContaining({
      id: 1,
      name: 'Alice',
    }),
  )
})
```

`expect.objectContaining` 忽略未列出的属性，适合验证对象中的关键字段。
