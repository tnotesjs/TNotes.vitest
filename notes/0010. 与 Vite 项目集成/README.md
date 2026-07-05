# [0010. 与 Vite 项目集成](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0010.%20%E4%B8%8E%20Vite%20%E9%A1%B9%E7%9B%AE%E9%9B%86%E6%88%90)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 复用 vite.config.ts](#3-复用-viteconfigts)
  - [3.1. 测试专用配置](#31-测试专用配置)
- [4. 插件系统](#4-插件系统)
  - [4.1. 仅测试时生效的插件](#41-仅测试时生效的插件)
- [5. 别名解析](#5-别名解析)
  - [5.1. 使用 vite-tsconfig-paths](#51-使用-vite-tsconfig-paths)
- [6. CSS 与静态资源](#6-css-与静态资源)
- [7. 注意事项](#7-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 复用 vite.config.ts
- 测试专用配置
- 插件系统
- 别名解析
- CSS 与静态资源处理

## 2. 评价

Vitest 与 Vite 深度集成，自动继承 Vite 配置中的插件、别名、CSS 处理等。测试专用配置可通过 vitest.config.ts 或 vite.config.ts 的 test 字段定义。

## 3. 复用 vite.config.ts

Vitest 会自动读取项目根目录的 `vite.config.ts`，无需额外配置。这意味着以下 Vite 配置会自动被 Vitest 继承：

- 插件列表（`plugins`）
- 路径别名（`resolve.alias`）
- CSS 处理规则
- 静态资源处理
- 环境变量定义

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': '/src',
    },
  },
  define: {
    __APP_VERSION__: JSON.stringify('1.0.0'),
  },
})
```

运行 `vitest` 时，上述配置会自动生效。`@/utils` 在测试文件中同样会被解析为 `/src/utils`。

### 3.1. 测试专用配置

如果需要测试专用的配置（比如使用 jsdom 环境），可以创建 `vitest.config.ts`：

```typescript
// vitest.config.ts
import { defineConfig, mergeConfig } from 'vitest/config'
import viteConfig from './vite.config'

export default mergeConfig(
  viteConfig,
  defineConfig({
    test: {
      environment: 'jsdom',
      globals: true,
    },
  }),
)
```

也可以直接在 `vite.config.ts` 中添加 `test` 字段：

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'jsdom',
    globals: true,
  },
})
```

两种方式效果相同，选择一种保持项目一致即可。

## 4. 插件系统

Vitest 复用 Vite 的插件系统，项目中已配置的 Vite 插件在测试时会自动生效。

常见的 Vite 插件：

| 插件                      | 用途           | 测试中的作用       |
| ------------------------- | -------------- | ------------------ |
| `@vitejs/plugin-vue`      | Vue SFC 编译   | 测试 `.vue` 文件   |
| `@vitejs/plugin-react`    | React JSX 转换 | 测试 JSX/TSX       |
| `vite-plugin-svg-icons`   | SVG 图标       | 组件测试中处理 SVG |
| `unplugin-auto-import`    | 自动导入       | 测试中自动导入 API |
| `unplugin-vue-components` | 组件自动注册   | 测试中自动注册组件 |

注意：部分插件在测试环境中可能行为不同。如果遇到问题，可以在 `vitest.config.ts` 中单独配置插件。

### 4.1. 仅测试时生效的插件

如果某个插件只需要在测试时使用，可以在 `vitest.config.ts` 中单独添加：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  plugins: [
    // 这个插件只在测试时生效
  ],
  test: {
    // ...
  },
})
```

## 5. 别名解析

Vite 的 `resolve.alias` 配置会在 Vitest 中自动生效：

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import path from 'node:path'

export default defineConfig({
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src'),
      '@components': path.resolve(__dirname, 'src/components'),
      '@utils': path.resolve(__dirname, 'src/utils'),
    },
  },
})
```

```typescript
// 测试文件中可以直接使用别名
import { describe, it, expect } from 'vitest'
import { formatPrice } from '@utils/format'
import Button from '@components/Button.vue'
```

### 5.1. 使用 vite-tsconfig-paths

如果项目通过 `tsconfig.json` 的 `paths` 配置别名（而非 `vite.config.ts`），需要安装 `vite-tsconfig-paths` 插件：

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

```json
// tsconfig.json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"],
      "@utils/*": ["./src/utils/*"]
    }
  }
}
```

这样 Vitest 就能正确解析 `tsconfig.json` 中定义的路径别名。

## 6. CSS 与静态资源

Vite 的 CSS 处理和静态资源导入在 Vitest 中同样生效：

```typescript
// 如果组件中导入了 CSS 文件
import './Button.css' // Vitest 会处理为空对象，不会报错

// 导入图片
import logo from './logo.png' // 会转换为路径字符串
```

如果测试中不需要 CSS 处理，可以在配置中忽略：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  css: {
    modules: {
      classNameStrategy: 'non-scoped',
    },
  },
})
```

## 7. 注意事项

- Vitest 会复用 `vite.config.ts` 中的所有配置，包括 `define`、`resolve`、`css` 等
- 如果 `vite.config.ts` 中使用了需要浏览器环境的插件（如某些 SSR 插件），在 Vitest 的 Node 环境中可能会出错
- 创建 `vitest.config.ts` 后，Vitest 不再自动读取 `vite.config.ts`，需要手动合并
- 部分 Vite 插件依赖 `this.emitFile` 等构建钩子，测试环境中可能不可用
