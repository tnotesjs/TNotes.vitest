# [0015. 测试跳过与筛选](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0015.%20%E6%B5%8B%E8%AF%95%E8%B7%B3%E8%BF%87%E4%B8%8E%E7%AD%9B%E9%80%89)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. test.skip](#3-testskip)
- [4. test.only](#4-testonly)
- [5. test.todo](#5-testtodo)
- [6. 通过名称筛选](#6-通过名称筛选)
- [7. 通过文件筛选](#7-通过文件筛选)
- [8. 动态条件跳过](#8-动态条件跳过)
- [9. skip 和 only 的组合](#9-skip-和-only-的组合)

<!-- endregion:toc -->

## 1. 本节内容

- test.skip 跳过测试
- test.only 只运行指定测试
- test.todo 标记待实现
- 通过名称筛选（-t 参数）
- 通过文件筛选
- 动态条件跳过

## 2. 评价

skip/only/todo 提供了灵活的测试控制能力。本地调试时使用 only 快速定位，CI 中应避免提交 only。todo 提醒开发者还有测试待实现。

## 3. test.skip

`test.skip` 跳过某个测试用例，不会执行也不会报错：

```typescript
import { test, expect } from 'vitest'

test('this runs', () => {
  expect(1 + 1).toBe(2)
})

test.skip('this is skipped', () => {
  expect(1 + 1).toBe(3) // 不会执行
})
```

`describe.skip` 可以跳过整个分组：

```typescript
describe.skip('skipped suite', () => {
  test('test 1', () => {})
  test('test 2', () => {})
})
```

常见使用场景：某个功能暂时有 bug，先跳过不阻塞 CI。

## 4. test.only

`test.only` 只运行标记的测试，其他测试全部跳过：

```typescript
import { test, expect } from 'vitest'

test('skipped 1', () => {
  expect(true).toBe(true)
})

test.only('only this runs', () => {
  expect(1 + 1).toBe(2)
})

test('skipped 2', () => {
  expect(true).toBe(true)
})
```

`describe.only` 只运行该分组内的测试：

```typescript
describe.only('this suite runs', () => {
  test('test 1', () => {})
  test('test 2', () => {})
})

describe('this suite is skipped', () => {
  test('test 3', () => {})
})
```

`only` 适合本地调试时快速定位某个测试。

## 5. test.todo

`test.todo` 标记一个待实现的测试：

```typescript
import { test, expect } from 'vitest'

test.todo('implement date formatting')
test.todo('handle edge cases')
```

`todo` 测试会在报告中显示为待办项，提醒开发者还有测试需要实现。

与 `skip` 的区别：`skip` 是已有测试但暂时跳过；`todo` 是测试还未编写。

## 6. 通过名称筛选

运行测试时可以通过 `-t` 参数按名称筛选：

```bash
# 只运行名称包含 "add" 的测试
vitest run -t "add"

# 使用正则表达式
vitest run -t "should.*user"

# 组合使用
vitest run src/utils.test.ts -t "format"
```

`-t` 参数会匹配 `test` 或 `describe` 的名称字符串，支持正则表达式。

## 7. 通过文件筛选

指定文件路径来运行特定文件的测试：

```bash
# 运行单个文件
vitest run src/utils.test.ts

# 运行目录
vitest run src/components

# 使用 glob
vitest run "src/**/*format*.test.ts"
```

## 8. 动态条件跳过

可以在测试函数内部根据条件动态跳过：

```typescript
import { test, expect } from 'vitest'

test('platform-specific test', ({ skip }) => {
  if (process.platform !== 'darwin') {
    skip()
    return
  }
  expect(true).toBe(true)
})
```

或者使用 `onTestFailed` 等回调组合实现更复杂的跳过逻辑。

## 9. skip 和 only 的组合

```typescript
describe('suite', () => {
  // 整个分组标记为 only
  test.only('runs', () => {})
  test('also runs', () => {}) // 在 only 分组内，也会执行
  test.skip('skipped', () => {}) // 显式 skip，不会执行
})
```

`skip` 的优先级高于 `only`。即使在 `only` 分组内，`skip` 标记的测试仍然不会执行。
