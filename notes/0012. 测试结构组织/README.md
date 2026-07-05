# [0012. 测试结构组织](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0012.%20%E6%B5%8B%E8%AF%95%E7%BB%93%E6%9E%84%E7%BB%84%E7%BB%87)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. describe 分组](#3-describe-分组)
- [4. test 和 it](#4-test-和-it)
- [5. 嵌套分组](#5-嵌套分组)
- [6. 顶层测试](#6-顶层测试)
- [7. 作用域](#7-作用域)

<!-- endregion:toc -->

## 1. 本节内容

- describe 分组
- test 和 it 定义测试用例
- 嵌套分组
- 顶层测试
- 作用域隔离

## 2. 评价

describe/test/it 构成了测试的基本结构。describe 用于逻辑分组，test/it 用于定义测试用例，嵌套分组可以清晰地组织复杂的测试场景。

## 3. describe 分组

`describe` 用于将相关的测试用例组织在一起：

```typescript
import { describe, test, expect } from 'vitest'

describe('String utils', () => {
  test('trim removes whitespace', () => {
    expect('  hello  '.trim()).toBe('hello')
  })

  test('toUpperCase converts to upper case', () => {
    expect('hello'.toUpperCase()).toBe('HELLO')
  })
})
```

`describe` 创建一个测试套件（suite），套件内的测试共享相同的上下文和生命周期钩子。

## 4. test 和 it

`test` 和 `it` 功能完全相同，都是定义一个测试用例：

```typescript
describe('math', () => {
  test('addition', () => {
    expect(1 + 1).toBe(2)
  })

  it('subtraction', () => {
    expect(5 - 3).toBe(2)
  })
})
```

选择哪一种取决于团队习惯。常见搭配是 `describe` + `it` 形成自然语言：`describe('Calculator') { it('should add numbers') }`。

## 5. 嵌套分组

`describe` 可以嵌套，形成层级结构：

```typescript
describe('Array', () => {
  describe('push', () => {
    test('adds element to end', () => {
      const arr = [1, 2]
      arr.push(3)
      expect(arr).toEqual([1, 2, 3])
    })

    test('returns new length', () => {
      expect([1, 2].push(3)).toBe(3)
    })
  })

  describe('pop', () => {
    test('removes last element', () => {
      const arr = [1, 2, 3]
      expect(arr.pop()).toBe(3)
      expect(arr).toEqual([1, 2])
    })
  })
})
```

嵌套层级没有限制，但建议不超过 3 层以保持可读性。

## 6. 顶层测试

测试文件中也可以不使用 `describe`，直接写顶层 `test`：

```typescript
// math.test.ts
import { test, expect } from 'vitest'

test('adds two numbers', () => {
  expect(1 + 1).toBe(2)
})

test('subtracts two numbers', () => {
  expect(5 - 3).toBe(2)
})
```

这种方式适合简单的工具函数测试。当测试用例较多或需要分组时，建议使用 `describe` 组织。

## 7. 作用域

`describe` 内定义的变量只在其回调函数内可见，不同 `describe` 之间相互隔离：

```typescript
describe('scope A', () => {
  const value = 'A'

  test('has access to value', () => {
    expect(value).toBe('A')
  })
})

describe('scope B', () => {
  // 这里无法访问 scope A 中的 value
  const value = 'B'

  test('has own value', () => {
    expect(value).toBe('B')
  })
})
```
