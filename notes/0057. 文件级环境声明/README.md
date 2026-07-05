# [0057. 文件级环境声明](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0057.%20%E6%96%87%E4%BB%B6%E7%BA%A7%E7%8E%AF%E5%A2%83%E5%A3%B0%E6%98%8E)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. @vitest-environment 注释](#3-vitest-environment-注释)
- [4. 支持的环境](#4-支持的环境)
- [5. 优先级](#5-优先级)
- [6. 使用场景](#6-使用场景)
- [7. 注释格式](#7-注释格式)
- [8. 与 describe 配合](#8-与-describe-配合)
- [9. TypeScript 类型](#9-typescript-类型)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- @vitest-environment 注释声明环境
- 支持的环境类型
- 优先级（注释高于配置文件）
- 使用场景
- 注释格式要求

## 2. 评价

文件级环境声明允许在测试文件顶部通过注释指定环境，优先级高于配置文件。适合项目中大部分测试用 node，少数需要 DOM 的场景。

## 3. @vitest-environment 注释

在测试文件顶部使用注释声明该文件使用的测试环境：

```typescript
// @vitest-environment jsdom
import { test, expect } from 'vitest'

test('DOM test', () => {
  document.body.innerHTML = '<div id="app"></div>'
  expect(document.getElementById('app')).toBeDefined()
})
```

## 4. 支持的环境

```typescript
// @vitest-environment node
// @vitest-environment jsdom
// @vitest-environment happy-dom
// @vitest-environment edge-runtime
```

## 5. 优先级

文件级注释的优先级高于配置文件中的 `environment`：

```typescript
// vitest.config.ts - 默认 node 环境
test: {
  environment: 'node',
}
```

```typescript
// @vitest-environment jsdom - 这个文件使用 jsdom
import { test, expect } from 'vitest'
```

## 6. 使用场景

项目中大部分测试使用 `node` 环境，只有组件测试需要 DOM 环境：

```
src/
├── utils/
│   └── math.test.ts          # node 环境（默认）
├── components/
│   └── Button.test.ts        # @vitest-environment jsdom
```

## 7. 注释格式

注释必须在文件的第一行（或 shebang 之后）：

```typescript
// @vitest-environment jsdom
import { test, expect } from 'vitest'
```

```typescript
#!/usr/bin/env node
// @vitest-environment jsdom
import { test, expect } from 'vitest'
```

## 8. 与 describe 配合

注释是文件级别的，不能在 `describe` 内切换环境。如果需要不同环境的测试，应分成不同文件。

## 9. TypeScript 类型

使用文件级环境声明时，TypeScript 需要知道当前环境的类型：

```json
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  }
}
```

## 10. 注意事项

- 注释必须在文件顶部，不能有空行
- 注释格式必须严格匹配 `// @vitest-environment <env>`
- 整个文件都在声明的环境中运行
