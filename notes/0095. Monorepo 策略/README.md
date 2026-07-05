# [0095. Monorepo 策略](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0095.%20Monorepo%20%E7%AD%96%E7%95%A5)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. pnpm workspace 结构](#3-pnpm-workspace-结构)
- [4. pnpm-workspace.yaml](#4-pnpm-workspaceyaml)
- [5. 根目录配置](#5-根目录配置)
- [6. 包级配置](#6-包级配置)
- [7. 共享配置](#7-共享配置)
- [8. 使用共享配置](#8-使用共享配置)
- [9. 根目录运行](#9-根目录运行)
- [10. CI 配置](#10-ci-配置)

<!-- endregion:toc -->

## 1. 本节内容

- pnpm workspace 结构
- pnpm-workspace.yaml
- 根目录配置
- 包级配置
- 共享配置
- 根目录运行
- CI 配置

## 2. 评价

Monorepo 使用 Workspace 统一管理测试。根目录配置共享设置，各包可以有独立配置。

## 3. pnpm workspace 结构

```
my-monorepo/
├── vitest.workspace.ts
├── pnpm-workspace.yaml
├── packages/
│   ├── core/
│   │   ├── package.json
│   │   ├── vitest.config.ts
│   │   └── src/
│   ├── ui/
│   │   ├── package.json
│   │   ├── vitest.config.ts
│   │   └── src/
│   └── utils/
│       ├── package.json
│       └── src/
```

## 4. pnpm-workspace.yaml

```yaml
packages:
  - 'packages/*'
```

## 5. 根目录配置

```typescript
// vitest.workspace.ts
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace([
  'packages/core',
  'packages/ui',
  'packages/utils',
])
```

## 6. 包级配置

每个包可以有独立的 `vitest.config.ts`：

```typescript
// packages/core/vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'node',
  },
})
```

## 7. 共享配置

创建共享配置包：

```typescript
// packages/test-config/index.ts
import { defineConfig } from 'vitest/config'

export const baseConfig = defineConfig({
  test: {
    globals: true,
    coverage: {
      provider: 'v8',
    },
  },
})
```

## 8. 使用共享配置

```typescript
// packages/core/vitest.config.ts
import { defineConfig, mergeConfig } from 'vitest/config'
import { baseConfig } from '@my-project/test-config'

export default mergeConfig(
  baseConfig,
  defineConfig({
    test: {
      environment: 'node',
    },
  }),
)
```

## 9. 根目录运行

```bash
# 运行所有包的测试
vitest run

# 运行指定包
vitest run --project core

# 运行覆盖率
vitest run --coverage
```

## 10. CI 配置

```yaml
- name: Install dependencies
  run: pnpm install

- name: Run all tests
  run: vitest run

- name: Run with coverage
  run: vitest run --coverage
```
