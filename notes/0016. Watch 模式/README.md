# [0016. Watch 模式](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0016.%20Watch%20%E6%A8%A1%E5%BC%8F)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 启动 Watch 模式](#3-启动-watch-模式)
- [4. 文件监听机制](#4-文件监听机制)
- [5. 交互式过滤](#5-交互式过滤)
- [6. 增量测试](#6-增量测试)
- [7. Watch 模式下的覆盖率](#7-watch-模式下的覆盖率)
- [8. 与编辑器集成](#8-与编辑器集成)
- [9. 配置 Watch 行为](#9-配置-watch-行为)

<!-- endregion:toc -->

## 1. 本节内容

- 启动 Watch 模式
- 文件监听机制
- 交互式过滤
- 增量测试
- 与编辑器集成

## 2. 评价

Watch 模式是本地开发的核心功能，文件变化后自动重跑受影响的测试。交互式过滤可以快速定位特定测试，增量测试确保反馈速度。

## 3. 启动 Watch 模式

运行 `vitest` 命令（不带 `run`）默认进入 Watch 模式：

```bash
vitest
```

Watch 模式下，Vitest 会监听文件变化，自动重新运行受影响的测试。

## 4. 文件监听机制

Vitest 使用 Vite 的文件监听能力，当源文件或测试文件发生变化时：

- 如果测试文件改变，重新运行该测试文件
- 如果源文件改变，重新运行依赖该源文件的所有测试
- 使用模块依赖图（dependency graph）来确定影响范围

## 5. 交互式过滤

进入 Watch 模式后，按下特定键可以进行交互操作：

| 按键    | 功能                             |
| ------- | -------------------------------- |
| `a`     | 运行所有测试                     |
| `f`     | 只运行上次失败的测试             |
| `t`     | 按名称过滤测试（输入正则表达式） |
| `p`     | 按文件名过滤                     |
| `q`     | 退出 Watch 模式                  |
| `Enter` | 重新运行所有测试                 |

按下 `t` 后输入名称模式：

```
filter > add
```

只运行名称包含 "add" 的测试。过滤结果会持续生效，直到清除。

## 6. 增量测试

增量测试是 Watch 模式的核心能力。Vitest 只重新运行受影响的测试，而不是全部重新运行。

影响范围的计算：

```
src/utils.ts 改变
  -> utils.test.ts 依赖 utils.ts
  -> 重新运行 utils.test.ts

src/components/Button.tsx 改变
  -> Button.test.ts 依赖 Button.tsx
  -> 重新运行 Button.test.ts
```

这使得开发过程中的测试反馈非常快速，通常在毫秒级完成。

## 7. Watch 模式下的覆盖率

Watch 模式下也可以查看覆盖率：

```bash
vitest --coverage
```

覆盖率报告会随每次重新运行而更新。如果只需要增量覆盖率，可以配置 `coverage.reportOnFailure`。

## 8. 与编辑器集成

VS Code 中使用 Vitest 扩展时，Watch 模式会在后台自动运行，测试结果直接显示在编辑器中。

也可以通过 `vitest --ui` 启动 Web UI，在浏览器中查看实时测试结果。

## 9. 配置 Watch 行为

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    watch: true, // 默认值
    watchExclude: ['**/node_modules/**', '**/dist/**'],
  },
})
```

`watchExclude` 用于排除不需要监听的文件或目录。
