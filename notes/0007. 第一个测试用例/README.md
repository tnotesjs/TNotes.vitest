# [0007. 第一个测试用例](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0007.%20%E7%AC%AC%E4%B8%80%E4%B8%AA%E6%B5%8B%E8%AF%95%E7%94%A8%E4%BE%8B)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. test 函数](#3-test-函数)
- [4. it 函数](#4-it-函数)
- [5. expect 断言](#5-expect-断言)
- [6. 测试文件命名规则](#6-测试文件命名规则)
- [7. 完整示例](#7-完整示例)

<!-- endregion:toc -->

## 1. 本节内容

- test 函数定义测试用例
- it 函数是 test 的别名
- expect 配合 matcher 进行断言
- 测试文件命名规则（.test.ts / .spec.ts）

## 2. 评价

test/it/expect 是 Vitest 最基础的三个 API，掌握它们就能编写简单的测试用例。测试文件命名遵循社区惯例，以 .test.ts 或 .spec.ts 结尾。

## 3. test 函数

`test` 是定义一个测试用例的基本函数：

```typescript
import { test, expect } from 'vitest'

test('1 加 1 等于 2', () => {
  expect(1 + 1).toBe(2)
})
```

`test` 接收三个参数：

- 第一个参数：测试名称（字符串）
- 第二个参数：测试函数（同步或异步）
- 第三个参数（可选）：超时时间（毫秒）

## 4. it 函数

`it` 是 `test` 的别名，功能完全相同：

```typescript
import { it, expect } from 'vitest'

it('1 加 1 等于 2', () => {
  expect(1 + 1).toBe(2)
})
```

两者选择哪一种取决于团队风格偏好。部分团队用 `it` 配合 `describe` 形成自然语言：

```typescript
describe('math', () => {
  it('should add two numbers', () => {
    expect(1 + 1).toBe(2)
  })
})
```

## 5. expect 断言

`expect` 用于创建断言，配合 matcher 方法验证结果：

```typescript
import { test, expect } from 'vitest'

test('expect 基础用法', () => {
  expect(2 + 2).toBe(4) // 严格相等
  expect({ a: 1 }).toEqual({ a: 1 }) // 深度相等
  expect(true).toBeTruthy() // 真值
  expect(null).toBeNull() // null
  expect([1, 2, 3]).toContain(2) // 包含
})
```

常用的 matcher：

| matcher           | 用途                                |
| ----------------- | ----------------------------------- |
| `toBe`            | 严格相等（`===`）                   |
| `toEqual`         | 深度相等（递归比较对象）            |
| `toStrictEqual`   | 严格深度相等（检查 undefined 属性） |
| `toContain`       | 数组或字符串包含                    |
| `toThrow`         | 验证抛出异常                        |
| `toBeInstanceOf`  | 类型检查                            |
| `toMatch`         | 正则匹配                            |
| `toMatchSnapshot` | 快照匹配                            |

## 6. 测试文件命名规则

Vitest 默认匹配以下文件名模式：

- `**/*.test.ts`
- `**/*.test.js`
- `**/*.spec.ts`
- `**/*.spec.js`
- 以及对应的 `.mts`、`.cts`、`.mjs`、`.cjs` 后缀

推荐的命名方式有两种：

方式一：测试文件与源文件同目录

```
src/
├── utils.ts
├── utils.test.ts       # 测试文件
├── components/
│   ├── Button.vue
│   └── Button.test.ts  # 测试文件
```

方式二：测试文件放在 `__tests__` 目录

```
src/
├── utils.ts
├── __tests__/
│   └── utils.test.ts
```

两种方式都可以，选择后保持项目一致即可。

## 7. 完整示例

以下是一个完整的测试文件示例：

```typescript
// math.ts
export function add(a: number, b: number): number {
  return a + b
}

export function divide(a: number, b: number): number {
  if (b === 0) {
    throw new Error('Division by zero')
  }
  return a / b
}
```

```typescript
// math.test.ts
import { describe, it, expect } from 'vitest'
import { add, divide } from './math'

describe('add', () => {
  it('两数相加', () => {
    expect(add(1, 2)).toBe(3)
  })

  it('负数相加', () => {
    expect(add(-1, -2)).toBe(-3)
  })

  it('与零相加', () => {
    expect(add(5, 0)).toBe(5)
  })
})

describe('divide', () => {
  it('正常除法', () => {
    expect(divide(10, 2)).toBe(5)
  })

  it('除以零抛出错误', () => {
    expect(() => divide(10, 0)).toThrow('Division by zero')
  })
})
```
