# [0124. 从 Mocha 与 Chai 迁移](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0124.%20%E4%BB%8E%20Mocha%20%E4%B8%8E%20Chai%20%E8%BF%81%E7%A7%BB)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. describe 和 it](#3-describe-和-it)
- [4. 断言替换](#4-断言替换)
- [5. assert 风格](#5-assert-风格)
- [6. 生命周期钩子](#6-生命周期钩子)
- [7. 异步测试](#7-异步测试)
- [8. 迁移步骤](#8-迁移步骤)
- [9. 类型支持](#9-类型支持)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- describe/it 完全兼容
- 断言替换（Chai -> expect）
- assert 风格
- 生命周期钩子
- 异步测试（done -> async/await）
- 迁移步骤

## 2. 评价

- Mocha/Chai 到 Vitest 的迁移需要替换断言风格
- describe/it 保持兼容，主要工作是将 Chai 断言替换为 expect 风格

## 3. describe 和 it

Mocha 的 `describe`/`it` 在 Vitest 中完全兼容：

```typescript
// Mocha
describe('math', () => {
  it('should add', () => {})
})

// Vitest - 完全相同
describe('math', () => {
  it('should add', () => {})
})
```

## 4. 断言替换

| Chai                            | Vitest (expect)             |
| ------------------------------- | --------------------------- |
| `expect(x).to.equal(y)`         | `expect(x).toBe(y)`         |
| `expect(x).to.deep.equal(y)`    | `expect(x).toEqual(y)`      |
| `expect(x).to.be.true`          | `expect(x).toBe(true)`      |
| `expect(x).to.have.lengthOf(3)` | `expect(x).toHaveLength(3)` |
| `expect(x).to.include(y)`       | `expect(x).toContain(y)`    |

## 5. assert 风格

```typescript
// Chai assert
assert.equal(actual, expected)
assert.deepEqual(actual, expected)

// Vitest expect
expect(actual).toBe(expected)
expect(actual).toEqual(expected)
```

## 6. 生命周期钩子

```typescript
// Mocha
before(() => {})
beforeEach(() => {})
afterEach(() => {})
after(() => {})

// Vitest - 相同
beforeAll(() => {})
beforeEach(() => {})
afterEach(() => {})
afterAll(() => {})
```

## 7. 异步测试

```typescript
// Mocha - callback
it('async', (done) => {
  fetchData((result) => {
    expect(result).to.equal('data')
    done()
  })
})

// Vitest - async/await
it('async', async () => {
  const result = await fetchData()
  expect(result).toBe('data')
})
```

## 8. 迁移步骤

1. 安装 Vitest
2. 替换 `expect` 断言
3. 替换 `assert` 断言
4. 移除 `done` 回调，使用 `async/await`

## 9. 类型支持

Vitest 内置 TypeScript 支持，不需要额外配置。

## 10. 注意事项

- Vitest 的 `expect` API 与 Jest 兼容
- Chai 的 `should` 风格需要额外配置
- 建议统一使用 `expect` 风格
