# [0068. 自定义序列化](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0068.%20%E8%87%AA%E5%AE%9A%E4%B9%89%E5%BA%8F%E5%88%97%E5%8C%96)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. snapshotSerializers](#3-snapshotserializers)
- [4. 序列化器结构](#4-序列化器结构)
- [5. test 方法](#5-test-方法)
- [6. serialize 方法](#6-serialize-方法)
- [7. 日期序列化示例](#7-日期序列化示例)
- [8. 使用第三方序列化器](#8-使用第三方序列化器)
- [9. 注册到 expect](#9-注册到-expect)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- snapshotSerializers 配置序列化器
- 序列化器结构（test/serialize）
- test 方法判断类型
- serialize 方法转换字符串
- 日期序列化示例
- 注册到 expect

## 2. 评价

自定义序列化器控制快照的序列化方式，适合特殊数据类型（如 Date、自定义类）。序列化器按注册顺序执行，第一个匹配的生效。

## 3. snapshotSerializers

`test.snapshotSerializers` 配置自定义序列化器：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    snapshotSerializers: ['./my-serializer.ts'],
  },
})
```

## 4. 序列化器结构

```typescript
// my-serializer.ts
import type { SnapshotSerializer } from 'vitest'

const serializer: SnapshotSerializer = {
  serialize(val, config, indentation, depth, refs, printer) {
    // 自定义序列化逻辑
    return `Custom(${val.toString()})`
  },
  test(val) {
    // 判断是否使用此序列化器
    return val instanceof MyClass
  },
}

export default serializer
```

## 5. test 方法

`test` 方法决定哪些值使用此序列化器：

```typescript
test(val) {
  // 返回 true 表示使用此序列化器
  return val instanceof Date
}
```

## 6. serialize 方法

`serialize` 方法将值转换为字符串：

```typescript
serialize(val, config, indentation, depth, refs, printer) {
  return `${val.getFullYear()}-${val.getMonth() + 1}-${val.getDate()}`
}
```

## 7. 日期序列化示例

```typescript
// date-serializer.ts
import type { SnapshotSerializer } from 'vitest'

const serializer: SnapshotSerializer = {
  serialize(val: Date) {
    return val.toISOString()
  },
  test(val) {
    return val instanceof Date
  },
}

export default serializer
```

## 8. 使用第三方序列化器

```bash
pnpm add -D pretty-format
```

```typescript
import { prettyFormat } from 'pretty-format'

const serializer = {
  serialize(val) {
    return prettyFormat(val)
  },
  test(val) {
    return typeof val === 'object'
  },
}
```

## 9. 注册到 expect

也可以通过 `expect.addSnapshotSerializer` 注册：

```typescript
import { expect } from 'vitest'

expect.addSnapshotSerializer({
  serialize(val) {
    return `Custom(${JSON.stringify(val)})`
  },
  test(val) {
    return val instanceof MyClass
  },
})
```

## 10. 注意事项

- 序列化器按注册顺序执行
- 第一个 `test` 返回 `true` 的序列化器会被使用
- 自定义序列化器影响所有快照
