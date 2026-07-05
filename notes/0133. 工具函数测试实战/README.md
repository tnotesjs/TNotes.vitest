# [0133. 工具函数测试实战](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0133.%20%E5%B7%A5%E5%85%B7%E5%87%BD%E6%95%B0%E6%B5%8B%E8%AF%95%E5%AE%9E%E6%88%98)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 纯函数测试](#3-纯函数测试)
- [4. 边界条件](#4-边界条件)
- [5. 异常路径](#5-异常路径)
- [6. 字符串处理](#6-字符串处理)
- [7. 数组操作](#7-数组操作)
- [8. 对象操作](#8-对象操作)
- [9. 异步函数](#9-异步函数)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 纯函数测试（输入/输出）
- 边界条件（极值/空值）
- 异常路径（throw）
- 字符串处理
- 数组操作
- 对象操作
- 异步函数

## 2. 评价

工具函数测试是最基础的测试类型。重点测试正常路径、边界条件和异常路径，确保函数在各种情况下都能正确工作。

## 3. 纯函数测试

```typescript
// math.ts
export function add(a: number, b: number): number {
  return a + b
}

// math.test.ts
import { add } from './math'

test('adds two numbers', () => {
  expect(add(1, 2)).toBe(3)
  expect(add(-1, 1)).toBe(0)
  expect(add(0, 0)).toBe(0)
})
```

## 4. 边界条件

```typescript
test('handles edge cases', () => {
  expect(add(Number.MAX_SAFE_INTEGER, 1)).toBeDefined()
  expect(add(Number.MIN_SAFE_INTEGER, -1)).toBeDefined()
})
```

## 5. 异常路径

```typescript
function divide(a: number, b: number): number {
  if (b === 0) {
    throw new Error('Division by zero')
  }
  return a / b
}

test('throws on division by zero', () => {
  expect(() => divide(10, 0)).toThrow('Division by zero')
})
```

## 6. 字符串处理

```typescript
function capitalize(str: string): string {
  if (!str) return ''
  return str.charAt(0).toUpperCase() + str.slice(1)
}

test('capitalizes string', () => {
  expect(capitalize('hello')).toBe('Hello')
  expect(capitalize('')).toBe('')
  expect(capitalize('a')).toBe('A')
})
```

## 7. 数组操作

```typescript
function unique<T>(arr: T[]): T[] {
  return [...new Set(arr)]
}

test('removes duplicates', () => {
  expect(unique([1, 2, 2, 3])).toEqual([1, 2, 3])
  expect(unique([])).toEqual([])
})
```

## 8. 对象操作

```typescript
function pick<T extends object, K extends keyof T>(
  obj: T,
  keys: K[],
): Pick<T, K> {
  const result = {} as Pick<T, K>
  keys.forEach((key) => {
    if (key in obj) {
      result[key] = obj[key]
    }
  })
  return result
}

test('picks properties', () => {
  const obj = { a: 1, b: 2, c: 3 }
  expect(pick(obj, ['a', 'c'])).toEqual({ a: 1, c: 3 })
})
```

## 9. 异步函数

```typescript
async function fetchData(url: string): Promise<Response> {
  const response = await fetch(url)
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`)
  }
  return response
}
```

## 10. 注意事项

- 测试正常路径和异常路径
- 测试边界条件
- 使用描述性的测试名称
