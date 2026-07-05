# [0137. Monorepo 测试实战](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0137.%20Monorepo%20%E6%B5%8B%E8%AF%95%E5%AE%9E%E6%88%98)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 项目结构](#3-项目结构)
- [4. Workspace 配置](#4-workspace-配置)
- [5. 共享配置](#5-共享配置)
- [6. 包级配置](#6-包级配置)
- [7. 运行测试](#7-运行测试)
- [8. 包间依赖测试](#8-包间依赖测试)
- [9. CI 配置](#9-ci-配置)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 项目结构（packages/core/ui/shared）
- Workspace 配置（vitest.workspace.ts）
- 共享配置（mergeConfig）
- 包级配置
- 运行测试
- 包间依赖测试
- CI 配置

## 2. 评价

Monorepo 使用 Workspace 统一管理测试。根目录配置共享设置，各包可以有独立配置。包间依赖测试确保集成正确。

## 3. 项目结构

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
│   └── shared/
│       ├── package.json
│       └── src/
```

## 4. Workspace 配置

```typescript
// vitest.workspace.ts
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace([
  'packages/core',
  'packages/ui',
  'packages/shared',
])
```

## 5. 共享配置

```typescript
// config/vitest.base.ts
import { defineConfig } from 'vitest/config'

export const baseConfig = defineConfig({
  test: {
    globals: true,
    clearMocks: true,
    restoreMocks: true,
  },
})
```

## 6. 包级配置

```typescript
// packages/core/vitest.config.ts
import { defineConfig, mergeConfig } from 'vitest/config'
import { baseConfig } from '../../config/vitest.base'

export default mergeConfig(
  baseConfig,
  defineConfig({
    test: {
      environment: 'node',
    },
  }),
)
```

## 7. 运行测试

```bash
# 运行所有包
vitest run

# 运行特定包
vitest run --project core

# 运行覆盖率
vitest run --coverage
```

## 8. 包间依赖测试

```typescript
// packages/ui/src/Button.test.ts
import { formatText } from '@my-project/core' // 依赖 core 包

test('formats text', () => {
  expect(formatText('hello')).toBe('Hello')
})
```

## 9. CI 配置

```yaml
- name: Run all tests
  run: pnpm vitest run --coverage
```

## 10. 注意事项

- 每个包可以有独立的测试配置
- 使用 Workspace 统一运行
- 共享配置减少重复
