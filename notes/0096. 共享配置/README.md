# [0096. 共享配置](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0096.%20%E5%85%B1%E4%BA%AB%E9%85%8D%E7%BD%AE)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. mergeConfig](#3-mergeconfig)
- [4. 基础配置抽取](#4-基础配置抽取)
- [5. 使用基础配置](#5-使用基础配置)
- [6. 配置优先级](#6-配置优先级)
- [7. 配置包](#7-配置包)
- [8. 使用配置包](#8-使用配置包)
- [9. Workspace 中使用](#9-workspace-中使用)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- mergeConfig 合并配置
- 基础配置抽取
- 使用基础配置
- 配置优先级
- 配置包
- Workspace 中使用

## 2. 评价

mergeConfig 用于合并多个配置对象，推荐将通用配置抽取到共享包，减少重复配置。

## 3. mergeConfig

`mergeConfig` 用于合并多个配置对象：

```typescript
import { defineConfig, mergeConfig } from 'vitest/config'

const baseConfig = defineConfig({
  test: {
    globals: true,
    coverage: {
      provider: 'v8',
    },
  },
})

const projectConfig = defineConfig({
  test: {
    environment: 'jsdom',
  },
})

export default mergeConfig(baseConfig, projectConfig)
```

## 4. 基础配置抽取

创建基础配置文件：

```typescript
// config/vitest.base.ts
import { defineConfig } from 'vitest/config'

export const baseConfig = defineConfig({
  test: {
    globals: true,
    clearMocks: true,
    restoreMocks: true,
    coverage: {
      provider: 'v8',
      reporter: ['text', 'html'],
    },
  },
})
```

## 5. 使用基础配置

```typescript
// packages/ui/vitest.config.ts
import { defineConfig, mergeConfig } from 'vitest/config'
import { baseConfig } from '../../config/vitest.base'

export default mergeConfig(
  baseConfig,
  defineConfig({
    test: {
      environment: 'jsdom',
      include: ['src/**/*.test.tsx'],
    },
  }),
)
```

## 6. 配置优先级

`mergeConfig` 的合并规则：

- 后面的配置覆盖前面的配置
- 数组会追加而非覆盖
- 对象会深度合并

## 7. 配置包

创建专门的配置包：

```typescript
// packages/vitest-config/index.ts
import { defineConfig } from 'vitest/config'

export const nodeConfig = defineConfig({
  test: { environment: 'node' },
})

export const jsdomConfig = defineConfig({
  test: { environment: 'jsdom' },
})

export const browserConfig = defineConfig({
  test: {
    browser: {
      enabled: true,
      provider: 'playwright',
    },
  },
})
```

## 8. 使用配置包

```typescript
// packages/core/vitest.config.ts
import { mergeConfig, defineConfig } from 'vitest/config'
import { nodeConfig } from '@my-project/vitest-config'

export default mergeConfig(
  nodeConfig,
  defineConfig({
    test: {
      include: ['src/**/*.test.ts'],
    },
  }),
)
```

## 9. Workspace 中使用

```typescript
// vitest.workspace.ts
import { defineWorkspace } from 'vitest/config'
import { nodeConfig, jsdomConfig } from '@my-project/vitest-config'

export default defineWorkspace([
  {
    extends: nodeConfig,
    name: 'core',
    root: './packages/core',
  },
  {
    extends: jsdomConfig,
    name: 'ui',
    root: './packages/ui',
  },
])
```

## 10. 注意事项

- `mergeConfig` 是深度合并
- 数组配置会追加
- 建议将通用配置抽取到共享包
