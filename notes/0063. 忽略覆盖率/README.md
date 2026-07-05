# [0063. 忽略覆盖率](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0063.%20%E5%BF%BD%E7%95%A5%E8%A6%86%E7%9B%96%E7%8E%87)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 注释忽略（v8）](#3-注释忽略v8)
- [4. 注释忽略（istanbul）](#4-注释忽略istanbul)
- [5. 文件忽略](#5-文件忽略)
- [6. 目录忽略](#6-目录忽略)
- [7. 分支忽略](#7-分支忽略)
- [8. 函数忽略](#8-函数忽略)
- [9. 条件忽略](#9-条件忽略)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- v8 注释忽略（v8 ignore）
- istanbul 注释忽略
- 文件忽略（exclude）
- 目录忽略
- 分支忽略
- 函数忽略
- 条件忽略

## 2. 评价

忽略覆盖率应该有明确理由，不是为了提高数字。注释忽略可以精确控制，文件排除适合整个文件不需要测试的场景。

## 3. 注释忽略（v8）

使用 `v8 ignore` 注释忽略特定代码：

```typescript
// 忽略下一行
/* v8 ignore next */
const debug = process.env.DEBUG

// 忽略代码块
/* v8 ignore start */
if (process.env.NODE_ENV === 'development') {
  console.log('debug info')
}
/* v8 ignore stop */
```

## 4. 注释忽略（istanbul）

使用 `istanbul ignore` 注释：

```typescript
/* istanbul ignore next */
const value = someFunction()

/* istanbul ignore start */
if (condition) {
  // 忽略
}
/* istanbul ignore stop */
```

## 5. 文件忽略

在配置中排除整个文件：

```typescript
coverage: {
  exclude: [
    'src/**/*.test.ts',
    'src/**/*.spec.ts',
    'src/**/*.d.ts',
    'src/**/types.ts',
    'src/**/constants.ts',
  ],
}
```

## 6. 目录忽略

```typescript
coverage: {
  exclude: [
    'src/**/migrations/**',
    'src/**/generated/**',
    'src/**/__mocks__/**',
  ],
}
```

## 7. 分支忽略

忽略特定的分支：

```typescript
function process(data: unknown) {
  /* v8 ignore next 3 */
  if (data === undefined) {
    return null
  }
  return data.toString()
}
```

`/* v8 ignore next 3 */` 忽略接下来的 3 行。

## 8. 函数忽略

```typescript
// 忽略整个函数
/* v8 ignore next */
function debugLog(message: string) {
  console.log(message)
}
```

## 9. 条件忽略

```typescript
// 只在特定条件下忽略
const isDev = process.env.NODE_ENV === 'development'
/* v8 ignore if: isDev */
if (isDev) {
  console.log('development mode')
} else {
  /* v8 ignore else */
  console.log('production mode')
}
```

## 10. 注意事项

- 忽略覆盖率应该是有意为之，不是为了提高数字
- 被忽略的代码不会计入覆盖率统计
- 建议在注释中说明忽略原因
