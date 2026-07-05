# [0086. 配置方式](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0086.%20%E9%85%8D%E7%BD%AE%E6%96%B9%E5%BC%8F)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. includeSource](#3-includesource)
- [4. 文件匹配](#4-文件匹配)
- [5. 生产构建剔除](#5-生产构建剔除)
- [6. Vite 配置](#6-vite-配置)
- [7. Rollup 配置](#7-rollup-配置)
- [8. 完整配置示例](#8-完整配置示例)
- [9. 运行测试](#9-运行测试)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- `includeSource` 配置
- 文件匹配
- 生产构建剔除
- Vite 配置
- 运行测试

## 2. 评价

In-source Testing 通过 `includeSource` 配置启用，构建工具会自动剔除测试代码。

## 3. includeSource

`test.includeSource` 启用 In-source Testing：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    includeSource: ['src/**/*.{js,ts}'],
  },
})
```

## 4. 文件匹配

`includeSource` 使用 glob 模式匹配源文件：

```typescript
includeSource: ['src/**/*.{js,ts}', 'src/**/*.ts', '!src/**/*.d.ts']
```

## 5. 生产构建剔除

构建工具会自动剔除测试代码：

```typescript
// math.ts
export function add(a: number, b: number): number {
  return a + b
}

// 测试代码在生产构建中会被完全移除
if (import.meta.vitest) {
  const { it, expect } = import.meta.vitest

  it('add', () => {
    expect(add(1, 2)).toBe(3)
  })
}
```

## 6. Vite 配置

Vite 会自动处理 `import.meta.vitest`：

```typescript
// vite.config.ts
import { defineConfig } from 'vite'

export default defineConfig({
  // 不需要额外配置，Vite 会自动处理
})
```

## 7. Rollup 配置

Rollup 也可以处理 `import.meta.vitest`：

```typescript
// rollup.config.js
export default {
  plugins: [
    // 使用 @rollup/plugin-replace 替换
  ],
}
```

## 8. 完整配置示例

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    includeSource: ['src/**/*.{js,ts}'],
  },
})
```

## 9. 运行测试

```bash
# 运行所有测试（包括 in-source）
vitest run

# 只运行 in-source 测试
vitest run --includeSource
```

## 10. 注意事项

- `includeSource` 与 `include` 是独立的配置
- 生产构建时测试代码会被剔除
- 建议只对简单函数使用 In-source Testing
