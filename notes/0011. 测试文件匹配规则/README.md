# [0011. 测试文件匹配规则](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0011.%20%E6%B5%8B%E8%AF%95%E6%96%87%E4%BB%B6%E5%8C%B9%E9%85%8D%E8%A7%84%E5%88%99)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 默认匹配规则](#3-默认匹配规则)
- [4. 自定义 include](#4-自定义-include)
- [5. exclude 排除规则](#5-exclude-排除规则)
- [6. 运行指定文件](#6-运行指定文件)
- [7. 文件扩展名](#7-文件扩展名)

<!-- endregion:toc -->

## 1. 本节内容

- 默认匹配规则（**tests** 目录和 .test/.spec 后缀）
- 自定义 include
- exclude 排除规则
- 运行指定文件
- 支持的文件扩展名

## 2. 评价

理解文件匹配规则有助于组织测试目录结构。默认规则覆盖了社区最常见的命名习惯，通过 include/exclude 可以灵活调整。

## 3. 默认匹配规则

Vitest 默认会匹配以下路径的文件：

```typescript
// 默认 include 配置
;['**/__tests__/**/*.[jt]s?(x)', '**/?(*.){test,spec}.[jt]s?(x)']
```

即：

- `__tests__` 目录下的 `.js`、`.ts`、`.jsx`、`.tsx` 文件
- 文件名以 `.test.js`、`.spec.ts` 等结尾的文件

这些默认规则覆盖了社区最常见的测试文件命名习惯。

## 4. 自定义 include

在配置文件中修改 `include` 来自定义匹配规则：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    include: ['src/**/*.{test,spec}.{js,ts}', 'tests/**/*.{test,spec}.{js,ts}'],
  },
})
```

支持 glob 模式，常用语法：

| 模式         | 说明                     |
| ------------ | ------------------------ |
| `*`          | 匹配单层目录中的任意字符 |
| `**`         | 匹配多层目录             |
| `{a,b}`      | 匹配 a 或 b              |
| `[abc]`      | 匹配括号内的任意字符     |
| `?(pattern)` | 匹配零次或一次           |

## 5. exclude 排除规则

`exclude` 用于排除不需要测试的文件：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    include: ['src/**/*.{test,spec}.{js,ts}'],
    exclude: ['node_modules', 'dist', 'build', '**/*.d.ts', '**/*.config.*'],
  },
})
```

`node_modules` 和 `dist` 是默认排除的目录，通常不需要显式配置。

## 6. 运行指定文件

除了通过配置文件控制匹配规则，也可以在命令行中直接指定文件：

```bash
# 运行单个文件
vitest run src/utils.test.ts

# 运行目录下所有测试
vitest run src/components

# 使用 glob 模式
vitest run "src/**/*.test.ts"
```

命令行指定的文件不受 `include`/`exclude` 配置限制。

## 7. 文件扩展名

Vitest 支持以下文件扩展名：

- `.js`、`.mjs`、`.cjs`
- `.ts`、`.mts`、`.cts`
- `.jsx`、`.tsx`

TypeScript 文件会通过 esbuild 自动转译，不需要预先编译。
