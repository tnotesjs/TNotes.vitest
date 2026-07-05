# [0042. TypeScript 路径别名](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0042.%20TypeScript%20%E8%B7%AF%E5%BE%84%E5%88%AB%E5%90%8D)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. tsconfig.json 中的 paths](#3-tsconfigjson-中的-paths)
- [4. vite-tsconfig-paths 插件](#4-vite-tsconfig-paths-插件)
- [5. 不使用插件的方式](#5-不使用插件的方式)
- [6. 推荐方案](#6-推荐方案)
- [7. Monorepo 中的路径别名](#7-monorepo-中的路径别名)
- [8. 注意事项](#8-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- tsconfig.json 中的 paths 配置
- vite-tsconfig-paths 插件
- 不使用插件的方式
- 推荐方案
- Monorepo 中的路径别名

## 2. 评价

推荐使用 vite-tsconfig-paths 插件自动解析 tsconfig.json 中的路径别名，避免手动维护两套别名配置。

## 3. tsconfig.json 中的 paths

TypeScript 项目通常在 `tsconfig.json` 中配置路径别名：

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@utils/*": ["./src/utils/*"],
      "@components/*": ["./src/components/*"]
    }
  }
}
```

这些别名在编辑器中可以正常工作，但 Vitest 不会自动读取 `tsconfig.json` 中的 `paths` 配置。

## 4. vite-tsconfig-paths 插件

安装 `vite-tsconfig-paths` 插件让 Vitest 识别 `tsconfig.json` 中的路径别名：

```bash
pnpm add -D vite-tsconfig-paths
```

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import tsconfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  plugins: [tsconfigPaths()],
})
```

配置后，测试文件中可以直接使用 `tsconfig.json` 中定义的别名：

```typescript
import { add } from '@utils/math'
import Button from '@components/Button.vue'
```

## 5. 不使用插件的方式

也可以在 `vite.config.ts` 中手动配置 `resolve.alias`：

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import path from 'node:path'

export default defineConfig({
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@utils': path.resolve(__dirname, 'src/utils'),
    },
  },
})
```

这种方式需要与 `tsconfig.json` 保持同步，维护成本更高。

## 6. 推荐方案

推荐使用 `vite-tsconfig-paths` 插件：

- 只需维护 `tsconfig.json` 一处配置
- 编辑器和 Vitest 行为一致
- 支持多个 `tsconfig.json` 文件

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import tsconfigPaths from 'vite-tsconfig-paths'

export default defineConfig({
  plugins: [
    tsconfigPaths({
      projects: ['./tsconfig.json'],
    }),
  ],
})
```

## 7. Monorepo 中的路径别名

Monorepo 项目中，每个子包可能有独立的 `tsconfig.json`：

```
packages/
├── core/
│   ├── tsconfig.json
│   └── vitest.config.ts
├── ui/
│   ├── tsconfig.json
│   └── vitest.config.ts
```

每个子包的 `vitest.config.ts` 中分别配置 `vite-tsconfig-paths`。

## 8. 注意事项

- `vite-tsconfig-paths` 会自动读取最近的 `tsconfig.json`
- 如果路径别名不生效，检查 `tsconfig.json` 中的 `baseUrl` 和 `paths` 配置
- 确保 `tsconfig.json` 中的路径与实际目录结构一致
