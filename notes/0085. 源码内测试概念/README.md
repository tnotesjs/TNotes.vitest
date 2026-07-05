# [0085. 源码内测试概念](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0085.%20%E6%BA%90%E7%A0%81%E5%86%85%E6%B5%8B%E8%AF%95%E6%A6%82%E5%BF%B5)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 什么是 In-source Testing](#3-什么是-in-source-testing)
- [4. 适用场景](#4-适用场景)
- [5. 优点](#5-优点)
- [6. 缺点](#6-缺点)
- [7. import.meta.vitest](#7-importmetavitest)
- [8. 与传统测试的对比](#8-与传统测试的对比)
- [9. 团队规范](#9-团队规范)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- In-source Testing 概念
- 适用场景
- 优点
- 缺点
- `import.meta.vitest`
- 与传统测试对比

## 2. 评价

In-source Testing 允许在源文件中直接编写测试，适合纯函数的简单验证。

## 3. 什么是 In-source Testing

In-source Testing 允许在源文件中直接编写测试代码，而不是创建单独的测试文件。

```typescript
// math.ts
export function add(a: number, b: number): number {
  return a + b
}

// 测试代码直接写在源文件中
if (import.meta.vitest) {
  const { describe, it, expect } = import.meta.vitest

  describe('add', () => {
    it('两数相加', () => {
      expect(add(1, 2)).toBe(3)
    })
  })
}
```

## 4. 适用场景

- 纯函数的简单测试
- 工具函数的快速验证
- 开发过程中的临时测试
- 文档式的使用示例

## 5. 优点

- 测试代码和源代码在一起，便于维护
- 不需要创建额外的测试文件
- 可以测试私有函数
- 适合快速原型开发

## 6. 缺点

- 增加源文件体积
- 可能影响代码可读性
- 团队需要统一规范
- 不适合复杂的测试场景

## 7. import.meta.vitest

`import.meta.vitest` 是 Vitest 提供的特殊变量：

- 测试环境中为 `true`
- 生产环境中为 `false`（或 undefined）

构建工具会根据这个变量进行 tree-shaking。

## 8. 与传统测试的对比

| 维度     | 传统测试     | In-source |
| -------- | ------------ | --------- |
| 文件位置 | 单独测试文件 | 源文件内  |
| 代码组织 | 分离         | 混合      |
| 私有函数 | 不可测试     | 可测试    |
| 适用场景 | 复杂测试     | 简单测试  |

## 9. 团队规范

使用 In-source Testing 需要团队统一规范：

- 测试代码放在文件末尾
- 使用 `import.meta.vitest` 判断
- 测试代码不影响生产构建

## 10. 注意事项

- 测试代码不会进入生产包
- 需要构建工具支持 `import.meta.vitest`
- 不适合大型测试套件
