# [0043. 外部依赖处理](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0043.%20%E5%A4%96%E9%83%A8%E4%BE%9D%E8%B5%96%E5%A4%84%E7%90%86)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. deps 配置](#3-deps-配置)
- [4. 内联依赖](#4-内联依赖)
- [5. 外部化依赖](#5-外部化依赖)
- [6. 优化依赖](#6-优化依赖)
- [7. interopDefault](#7-interopdefault)
- [8. 常见问题](#8-常见问题)
  - [8.1. 依赖不兼容 ESM](#81-依赖不兼容-esm)
  - [8.2. 依赖中有动态导入](#82-依赖中有动态导入)
  - [8.3. 依赖需要特殊环境](#83-依赖需要特殊环境)
- [9. moduleDirectories](#9-moduledirectories)

<!-- endregion:toc -->

## 1. 本节内容

- deps 配置控制依赖处理
- 内联依赖
- 外部化依赖
- 优化依赖
- interopDefault
- 常见问题及解决方案

## 2. 评价

Vitest 默认内联 node_modules 中的依赖以确保 ESM 兼容性。遇到兼容问题时可以通过 deps 配置调整处理方式。

## 3. deps 配置

`test.deps` 控制 Vitest 如何处理外部依赖：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    deps: {
      // 强制内联的依赖
      inline: [/some-package/],
      // 外部化的依赖
      external: [/external-package/],
    },
  },
})
```

## 4. 内联依赖

默认情况下，Vitest 会内联（转换）`node_modules` 中的依赖。内联意味着 Vitest 会像处理源码一样处理这些依赖：

```typescript
test: {
  deps: {
    inline: [
      // 正则匹配
      /lodash/,
      // 包名
      'vue',
      // 所有依赖都内联（默认行为）
      true,
    ],
  },
}
```

内联的优点：

- 确保 ESM 兼容性
- 可以对依赖进行 Mock
- 支持路径别名解析

## 5. 外部化依赖

将某些依赖标记为外部化，不进行转换：

```typescript
test: {
  deps: {
    external: [
      /native-module/,
      'sharp',
    ],
  },
}
```

外部化的依赖会使用 Node.js 原生模块系统加载。

## 6. 优化依赖

`deps.optimizer` 控制依赖的优化行为：

```typescript
test: {
  deps: {
    optimizer: {
      ssr: {
        enabled: true,
        include: ['lodash-es'],
      },
    },
  },
}
```

## 7. interopDefault

控制 CommonJS 模块的默认导出行为：

```typescript
test: {
  deps: {
    interopDefault: true, // 默认值
  },
}
```

设为 `true` 时，CommonJS 模块的 `module.exports` 会被自动包装为 `{ default: module.exports }`。

## 8. 常见问题

### 8.1. 依赖不兼容 ESM

某些旧版依赖只提供 CommonJS 格式，可能在 Vitest 中报错。解决方式：

```typescript
test: {
  deps: {
    inline: [/old-package/],
  },
}
```

### 8.2. 依赖中有动态导入

```typescript
test: {
  deps: {
    inline: [/package-with-dynamic-import/],
  },
}
```

### 8.3. 依赖需要特殊环境

某些依赖（如 `canvas`、`sharp`）需要原生模块，应标记为外部化：

```typescript
test: {
  deps: {
    external: ['canvas', 'sharp'],
  },
}
```

## 9. moduleDirectories

```typescript
test: {
  moduleDirectories: ['node_modules', 'src'],
}
```

指定模块查找目录，默认为 `['node_modules']`。
