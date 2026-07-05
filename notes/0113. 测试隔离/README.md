# [0113. 测试隔离](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0113.%20%E6%B5%8B%E8%AF%95%E9%9A%94%E7%A6%BB)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 为什么需要测试隔离](#3-为什么需要测试隔离)
- [4. 避免共享可变状态](#4-避免共享可变状态)
- [5. 每个测试独立数据](#5-每个测试独立数据)
- [6. 使用 beforeEach](#6-使用-beforeeach)
- [7. Mock 隔离](#7-mock-隔离)
- [8. 配置自动隔离](#8-配置自动隔离)
- [9. 文件级隔离](#9-文件级隔离)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 为什么需要测试隔离
- 避免共享可变状态
- 每个测试独立数据
- 使用 beforeEach 重置
- Mock 隔离
- 配置自动隔离

## 2. 评价

- 测试隔离是保证测试可靠性的基础。避免测试间共享可变状态，使用 beforeEach 重置数据，配置 restoreMocks 自动恢复。

## 3. 为什么需要测试隔离

测试之间共享可变状态会导致：

- 测试结果不确定
- 测试顺序影响结果
- 难以定位失败原因

## 4. 避免共享可变状态

```typescript
// 不好的做法
let counter = 0

test('test 1', () => {
  counter++
  expect(counter).toBe(1)
})

test('test 2', () => {
  counter++ // 受 test 1 影响
  expect(counter).toBe(2) // 可能失败
})
```

## 5. 每个测试独立数据

```typescript
// 好的做法
test('test 1', () => {
  const counter = 0
  expect(counter + 1).toBe(1)
})

test('test 2', () => {
  const counter = 0
  expect(counter + 1).toBe(1)
})
```

## 6. 使用 beforeEach

```typescript
describe('isolated', () => {
  let data: string[]

  beforeEach(() => {
    data = [] // 每个测试前重置
  })

  test('test 1', () => {
    data.push('a')
    expect(data).toEqual(['a'])
  })

  test('test 2', () => {
    expect(data).toEqual([]) // 独立的 data
  })
})
```

## 7. Mock 隔离

```typescript
afterEach(() => {
  vi.restoreAllMocks()
})
```

## 8. 配置自动隔离

```typescript
// vitest.config.ts
test: {
  clearMocks: true,
  restoreMocks: true,
  mockReset: true,
  unstubEnvs: true,
  unstubGlobals: true,
}
```

## 9. 文件级隔离

Vitest 默认每个测试文件在独立的上下文中运行。

## 10. 注意事项

- 避免在测试之间共享状态
- 使用 beforeEach 重置状态
- 配置自动清理 Mock
