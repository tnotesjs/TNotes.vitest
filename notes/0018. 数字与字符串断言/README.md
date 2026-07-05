# [0018. 数字与字符串断言](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0018.%20%E6%95%B0%E5%AD%97%E4%B8%8E%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%96%AD%E8%A8%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 数字比较](#3-数字比较)
  - [3.1. toBeGreaterThan / toBeGreaterThanOrEqual](#31-tobegreaterthan--tobegreaterthanorequal)
  - [3.2. toBeLessThan / toBeLessThanOrEqual](#32-tobelessthan--tobelessthanorequal)
  - [3.3. toBeCloseTo](#33-tobecloseto)
- [4. 字符串匹配](#4-字符串匹配)
  - [4.1. toContain](#41-tocontain)
  - [4.2. toMatch](#42-tomatch)
  - [4.3. toMatchObject（字符串场景）](#43-tomatchobject字符串场景)
- [5. toContain（数组场景）](#5-tocontain数组场景)
- [6. toHaveLength](#6-tohavelength)
- [7. toBeTypeOf](#7-tobetypeof)

<!-- endregion:toc -->

## 1. 本节内容

- 数字比较（toBeGreaterThan/toBeLessThan/toBeCloseTo）
- 字符串匹配（toContain/toMatch）
- 数组包含
- toHaveLength
- toBeTypeOf

## 2. 评价

数字和字符串断言覆盖了常见的比较场景。toBeCloseTo 解决浮点数精度问题，toMatch 支持正则匹配，toBeTypeOf 是 v4 新增的类型断言。

## 3. 数字比较

### 3.1. toBeGreaterThan / toBeGreaterThanOrEqual

```typescript
import { test, expect } from 'vitest'

test('greater than', () => {
  expect(10).toBeGreaterThan(5)
  expect(10).toBeGreaterThanOrEqual(10)
  expect(10).toBeGreaterThanOrEqual(9)
})
```

### 3.2. toBeLessThan / toBeLessThanOrEqual

```typescript
test('less than', () => {
  expect(5).toBeLessThan(10)
  expect(5).toBeLessThanOrEqual(5)
  expect(5).toBeLessThanOrEqual(6)
})
```

### 3.3. toBeCloseTo

`toBeCloseTo` 用于浮点数比较，避免精度问题：

```typescript
test('floating point', () => {
  expect(0.1 + 0.2).not.toBe(0.3) // 精度问题
  expect(0.1 + 0.2).toBeCloseTo(0.3) // 通过
  expect(0.1 + 0.2).toBeCloseTo(0.3, 10) // 指定精度（小数位数）
})
```

默认精度为 2 位小数。第二个参数指定小数位数的精度。

## 4. 字符串匹配

### 4.1. toContain

检查字符串是否包含子串：

```typescript
test('string contains', () => {
  expect('Hello, World!').toContain('World')
  expect('vitest is great').toContain('vitest')
  expect('abc').not.toContain('d')
})
```

### 4.2. toMatch

使用正则表达式匹配字符串：

```typescript
test('string matches regex', () => {
  expect('hello123').toMatch(/\d+/)
  expect('user@example.com').toMatch(/^[\w.-]+@[\w.-]+\.\w+$/)
  expect('Vitest v4').toMatch(/Vitest v\d/)
})
```

### 4.3. toMatchObject（字符串场景）

```typescript
test('string starts/ends with', () => {
  expect('Hello, World!').toMatch(/^Hello/)
  expect('Hello, World!').toMatch(/World!$/)
})
```

## 5. toContain（数组场景）

`toContain` 也可以用于数组：

```typescript
test('array contains', () => {
  expect([1, 2, 3]).toContain(2)
  expect(['a', 'b', 'c']).toContain('b')
  expect([1, 2, 3]).not.toContain(4)
})
```

`toContain` 使用 `Object.is` 进行比较。对于对象元素，使用 `toContainEqual`。

## 6. toHaveLength

```typescript
test('length check', () => {
  expect([1, 2, 3]).toHaveLength(3)
  expect('hello').toHaveLength(5)
  expect([]).toHaveLength(0)
})
```

## 7. toBeTypeOf

Vitest v4 新增的断言，检查值的类型：

```typescript
test('type check', () => {
  expect('hello').toBeTypeOf('string')
  expect(42).toBeTypeOf('number')
  expect(true).toBeTypeOf('boolean')
  expect(undefined).toBeTypeOf('undefined')
  expect(null).toBeTypeOf('object') // typeof null === 'object'
  expect(() => {}).toBeTypeOf('function')
})
```
