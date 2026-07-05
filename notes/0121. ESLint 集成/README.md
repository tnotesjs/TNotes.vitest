# [0121. ESLint 集成](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0121.%20ESLint%20%E9%9B%86%E6%88%90)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装](#3-安装)
- [4. 配置](#4-配置)
- [5. 常用规则](#5-常用规则)
- [6. 测试代码规范](#6-测试代码规范)
- [7. 禁止 focused tests](#7-禁止-focused-tests)
- [8. 统一风格](#8-统一风格)
- [9. TypeScript 支持](#9-typescript-支持)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 eslint-plugin-vitest
- 配置 eslint.config.js
- 常用规则（expect-expect/no-identical-title/no-focused-tests）
- 测试代码规范
- 禁止 focused tests
- 统一风格

## 2. 评价

- ESLint 集成可以在编码阶段发现测试代码的问题
- no-focused-tests 规则避免意外提交 test.only，是团队协作的重要保障

## 3. 安装

```bash
pnpm add -D eslint-plugin-vitest
```

## 4. 配置

```javascript
// eslint.config.js
import vitest from 'eslint-plugin-vitest'

export default [
  {
    plugins: {
      vitest,
    },
    rules: {
      'vitest/expect-expect': 'error',
      'vitest/no-identical-title': 'error',
      'vitest/no-focused-tests': 'error',
      'vitest/no-disabled-tests': 'warn',
    },
  },
]
```

## 5. 常用规则

| 规则                 | 说明                |
| -------------------- | ------------------- |
| `expect-expect`      | 确保测试中有断言    |
| `no-identical-title` | 禁止相同的测试标题  |
| `no-focused-tests`   | 禁止 `test.only`    |
| `no-disabled-tests`  | 警告 `test.skip`    |
| `consistent-test-it` | 统一使用 test 或 it |
| `prefer-to-be`       | 偏好使用 toBe       |

## 6. 测试代码规范

```typescript
// 推荐
test('should return user name', () => {
  const user = getUser(1)
  expect(user.name).toBe('Alice')
})

// 不推荐
test('test 1', () => {
  expect(getUser(1).name).toBe('Alice')
})
```

## 7. 禁止 focused tests

```typescript
// 错误 - 会被 ESLint 报错
test.only('focused', () => {})

// 错误 - 会被 ESLint 报错
describe.only('focused', () => {})
```

## 8. 统一风格

```typescript
// 配置 consistent-test-it
// 'vitest/consistent-test-it': ['error', { fn: 'test' }]

// 推荐
test('should work', () => {})

// 不推荐
it('should work', () => {})
```

## 9. TypeScript 支持

```typescript
// eslint.config.ts
import vitest from 'eslint-plugin-vitest'

export default [
  {
    files: ['**/*.test.ts'],
    plugins: {
      vitest,
    },
    rules: {
      'vitest/expect-expect': 'error',
    },
  },
]
```

## 10. 注意事项

- 在 CI 中运行 ESLint 检查测试代码
- 统一团队的测试代码风格
- 使用 `no-focused-tests` 避免意外提交 `test.only`
