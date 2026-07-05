# [0111. 库项目类型保障](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0111.%20%E5%BA%93%E9%A1%B9%E7%9B%AE%E7%B1%BB%E5%9E%8B%E4%BF%9D%E9%9A%9C)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 为什么需要类型保障](#3-为什么需要类型保障)
- [4. 测试公开 API](#4-测试公开-api)
- [5. 泛型约束](#5-泛型约束)
- [6. 重载函数](#6-重载函数)
- [7. 类型回归测试](#7-类型回归测试)
- [8. CI 集成](#8-ci-集成)
- [9. 版本发布前检查](#9-版本发布前检查)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 为什么需要类型保障
- 测试公开 API
- 泛型约束验证
- 重载函数测试
- 类型回归测试
- CI 集成
- 版本发布前检查

## 2. 评价

- 类型测试是库项目质量保障的重要环节，确保公开 API 的类型定义不被意外修改。建议在 CI 中运行类型测试。

## 3. 为什么需要类型保障

库的公开 API 类型可能被意外修改：

- 泛型约束被破坏
- 重载函数丢失
- 返回类型变弱
- 参数类型变宽

## 4. 测试公开 API

```typescript
// types.test-d.ts
import { expectTypeOf } from 'vitest'
import { createStore } from '../src'

// 验证返回类型
expectTypeOf(createStore(0)).toHaveProperty('getState')
expectTypeOf(createStore(0)).toHaveProperty('setState')
expectTypeOf(createStore(0).getState).returns.toBeNumber()
```

## 5. 泛型约束

```typescript
function identity<T>(value: T): T {
  return value
}

// 确保泛型约束正确
expectTypeOf(identity<number>)
  .parameter(0)
  .toBeNumber()
expectTypeOf(identity<number>).returns.toBeNumber()
expectTypeOf(identity<string>)
  .parameter(0)
  .toBeString()
expectTypeOf(identity<string>).returns.toBeString()
```

## 6. 重载函数

```typescript
function parse(input: string): object
function parse(input: Buffer): object
function parse(input: string | Buffer): object {
  return {}
}

expectTypeOf(parse).toBeFunction()
```

## 7. 类型回归测试

```typescript
// 每次修改后运行类型测试，确保没有破坏
describe('API types', () => {
  test('createStore returns correct types', () => {
    expectTypeOf(createStore(0)).toHaveProperty('getState')
    expectTypeOf(createStore(0)).toHaveProperty('setState')
    expectTypeOf(createStore(0)).toHaveProperty('subscribe')
  })
})
```

## 8. CI 集成

```yaml
- name: Type check
  run: pnpm tsc --noEmit

- name: Run type tests
  run: vitest run "src/**/*.test-d.ts"
```

## 9. 版本发布前检查

```bash
# 发布前运行类型测试
vitest run "src/**/*.test-d.ts"
pnpm tsc --noEmit
```

## 10. 注意事项

- 类型测试应在 CI 中运行
- 类型测试文件应与源码一起维护
- 重大类型变更需要更新类型测试
