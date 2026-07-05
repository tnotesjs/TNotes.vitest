# [0041. Vite 解析机制](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0041.%20Vite%20%E8%A7%A3%E6%9E%90%E6%9C%BA%E5%88%B6)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. alias 路径别名](#3-alias-路径别名)
- [4. plugins 插件系统](#4-plugins-插件系统)
- [5. transform 转换](#5-transform-转换)
- [6. define 全局常量](#6-define-全局常量)
- [7. css 配置](#7-css-配置)
- [8. envPrefix](#8-envprefix)

<!-- endregion:toc -->

## 1. 本节内容

- alias 路径别名
- plugins 插件系统
- transform 转换
- define 全局常量
- css 配置
- envPrefix

## 2. 评价

Vitest 复用 Vite 的完整解析机制，包括路径别名、插件链、文件转换等。测试中的模块解析行为与 Vite 构建一致。

## 3. alias 路径别名

Vite 的 `resolve.alias` 在 Vitest 中自动生效：

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
import { add } from '@utils/math'
import Button from '@components/Button.vue'
```

## 4. plugins 插件系统

Vitest 复用 Vite 的插件链，项目中已配置的插件在测试时自动生效：

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
})
```

常见插件在测试中的作用：

| 插件                    | 测试中的作用      |
| ----------------------- | ----------------- |
| `@vitejs/plugin-vue`    | 编译 `.vue` 文件  |
| `@vitejs/plugin-react`  | 转换 JSX/TSX      |
| `vite-plugin-svg-icons` | 处理 SVG 图标导入 |
| `unplugin-auto-import`  | 自动导入 API      |

## 5. transform 转换

Vite 的 transform 管道在 Vitest 中同样生效。所有经过 Vite 处理的文件类型（TypeScript、JSX、CSS、图片等）在测试中都能正常导入：

```typescript
// 导入 CSS - Vitest 会处理为空对象
import './styles.css'

// 导入图片 - 会转换为路径字符串
import logo from './logo.png'

// 导入 Vue SFC - 通过 @vitejs/plugin-vue 编译
import MyComponent from './MyComponent.vue'
```

## 6. define 全局常量

```typescript
// vite.config.ts
export default defineConfig({
  define: {
    __APP_VERSION__: JSON.stringify('1.0.0'),
    __DEV__: true,
  },
})
```

```typescript
// 测试中可以直接使用
test('global constant', () => {
  expect(__APP_VERSION__).toBe('1.0.0')
})
```

## 7. css 配置

Vitest 支持 Vite 的 CSS 处理配置：

```typescript
// vite.config.ts
export default defineConfig({
  css: {
    modules: {
      localsConvention: 'camelCase',
    },
  },
})
```

测试中导入 CSS 模块时，会按照配置进行转换。

## 8. envPrefix

```typescript
// vite.config.ts
export default defineConfig({
  envPrefix: 'VITE_',
})
```

以 `VITE_` 为前缀的环境变量会暴露到 `import.meta.env` 中，测试中同样生效。
