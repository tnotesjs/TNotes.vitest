# [0045. 配置文件入口](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0045.%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%85%A5%E5%8F%A3)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. vitest.config.ts](#3-vitestconfigts)
- [4. vite.config.ts 中的 test](#4-viteconfigts-中的-test)
- [5. 两种方式的区别](#5-两种方式的区别)
- [6. 合并配置](#6-合并配置)
- [7. 命令行指定配置](#7-命令行指定配置)
- [8. 配置文件的作用](#8-配置文件的作用)

<!-- endregion:toc -->

## 1. 本节内容

- vitest.config.ts 独立配置
- vite.config.ts 中的 test 字段
- 两种方式的区别
- 合并配置（mergeConfig）
- 命令行指定配置

## 2. 评价

推荐使用独立的 vitest.config.ts 文件，避免影响 Vite 构建配置。使用 mergeConfig 可以继承 Vite 配置并扩展测试专用选项。

## 3. vitest.config.ts

推荐创建独立的 `vitest.config.ts` 文件：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    environment: 'jsdom',
    globals: true,
  },
})
```

Vitest 启动时会按以下顺序查找配置文件：

1. `vitest.config.ts`
2. `vitest.config.mts`
3. `vitest.config.cts`
4. `vitest.config.js`
5. `vitest.config.mjs`
6. `vitest.config.cjs`

## 4. vite.config.ts 中的 test

也可以在 `vite.config.ts` 中添加 `test` 字段：

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

## 5. 两种方式的区别

| 方式                         | 说明                        |
| ---------------------------- | --------------------------- |
| `vitest.config.ts`           | 独立配置，不影响 Vite 构建  |
| `vite.config.ts` 中的 `test` | 共享配置，Vite 插件自动继承 |

如果同时存在两个文件，`vitest.config.ts` 优先。

## 6. 合并配置

使用 `mergeConfig` 合并 Vite 配置和 Vitest 配置：

```typescript
// vitest.config.ts
import { defineConfig, mergeConfig } from 'vitest/config'
import viteConfig from './vite.config'

export default mergeConfig(
  viteConfig,
  defineConfig({
    test: {
      environment: 'jsdom',
    },
  }),
)
```

## 7. 命令行指定配置

```bash
# 使用指定配置文件
vitest --config ./my-vitest.config.ts

# 使用不同环境
vitest --environment jsdom
```

## 8. 配置文件的作用

配置文件控制 Vitest 的所有行为：

- 测试文件匹配规则
- 测试环境
- 覆盖率设置
- 超时时间
- 并发策略
- setup 文件
- Reporter
