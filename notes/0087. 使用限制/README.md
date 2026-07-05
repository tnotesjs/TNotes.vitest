# [0087. 使用限制](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0087.%20%E4%BD%BF%E7%94%A8%E9%99%90%E5%88%B6)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Tree-shaking](#3-tree-shaking)
- [4. 代码体积](#4-代码体积)
- [5. 团队规范](#5-团队规范)
- [6. 复杂测试限制](#6-复杂测试限制)
- [7. 代码可读性](#7-代码可读性)
- [8. 构建工具支持](#8-构建工具支持)
- [9. 类型检查](#9-类型检查)
- [10. 使用建议](#10-使用建议)

<!-- endregion:toc -->

## 1. 本节内容

- Tree-shaking 剔除
- 代码体积
- 团队规范
- 复杂测试限制
- 构建工具支持

## 2. 评价

In-source Testing 有明确适用边界，不适合复杂测试场景。

## 3. Tree-shaking

生产构建时，构建工具会移除 `import.meta.vitest` 块内的代码：

```typescript
// 生产构建后
export function add(a: number, b: number): number {
  return a + b
}

// 测试代码被完全移除
```

## 4. 代码体积

In-source Testing 会增加源文件的体积，但在生产构建后会被剔除：

- 开发环境：包含测试代码
- 生产环境：不包含测试代码

## 5. 团队规范

使用 In-source Testing 需要团队达成共识：

- 何时使用 In-source Testing
- 测试代码的格式规范
- 测试代码的位置（文件末尾）

## 6. 复杂测试限制

In-source Testing 不适合复杂测试：

- 需要多个测试文件的场景
- 需要复杂 setup/teardown 的场景
- 需要大量 Mock 的场景

## 7. 代码可读性

测试代码可能影响源代码的可读性：

- 测试代码应简洁明了
- 测试代码应放在文件末尾
- 使用注释分隔测试代码

## 8. 构建工具支持

需要构建工具支持 `import.meta.vitest`：

- Vite：原生支持
- Rollup：需要配置
- Webpack：需要配置

## 9. 类型检查

TypeScript 可能不认识 `import.meta.vitest`：

```typescript
// 添加类型声明
interface ImportMeta {
  vitest?: typeof import('vitest')
}
```

## 10. 使用建议

- 纯函数的简单测试适合 In-source Testing
- 复杂测试使用传统测试文件
- 团队需要统一规范
- 保持测试代码简洁
