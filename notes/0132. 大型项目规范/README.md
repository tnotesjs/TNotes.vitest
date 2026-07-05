# [0132. 大型项目规范](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0132.%20%E5%A4%A7%E5%9E%8B%E9%A1%B9%E7%9B%AE%E8%A7%84%E8%8C%83)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 目录结构](#3-目录结构)
- [4. 共享测试工具](#4-共享测试工具)
- [5. 测试分层](#5-测试分层)
- [6. 命名规范](#6-命名规范)
- [7. 配置管理](#7-配置管理)
- [8. 代码组织](#8-代码组织)
- [9. 测试数据管理](#9-测试数据管理)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 目录结构（components/utils/hooks）
- 共享测试工具（**tests**/helpers）
- 测试分层（单元/集成/E2E）
- 命名规范
- 配置管理
- 测试数据管理（**fixtures**）

## 2. 评价

大型项目需要统一的测试规范，包括目录结构、命名规则、共享工具等。良好的规范可以提高团队协作效率。

## 3. 目录结构

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx
│   │   └── index.ts
│   └── index.ts
├── utils/
│   ├── math.ts
│   ├── math.test.ts
│   └── index.ts
├── hooks/
│   ├── useAuth.ts
│   ├── useAuth.test.ts
│   └── index.ts
└── __tests__/
    ├── setup.ts
    └── helpers.ts
```

## 4. 共享测试工具

```typescript
// __tests__/helpers.ts
export function createUser(overrides = {}) {
  return { id: 1, name: 'Alice', ...overrides }
}

export function createMockApi() {
  return {
    fetchUser: vi.fn(),
    fetchPosts: vi.fn(),
  }
}
```

## 5. 测试分层

- 单元测试：纯函数、工具库
- 组件测试：UI 组件
- 集成测试：模块协作
- E2E 测试：用户流程

## 6. 命名规范

```
src/utils/math.ts
src/utils/math.test.ts       # 运行时测试
src/utils/math.test-d.ts     # 类型测试
```

## 7. 配置管理

```typescript
// config/vitest.base.ts
export const baseConfig = {
  globals: true,
  clearMocks: true,
  restoreMocks: true,
}
```

## 8. 代码组织

每个测试文件对应一个源文件：

```
Button.tsx -> Button.test.tsx
useAuth.ts -> useAuth.test.ts
```

## 9. 测试数据管理

```
src/__fixtures__/
├── users.ts
├── posts.ts
└── index.ts
```

## 10. 注意事项

- 保持测试文件与源文件同步
- 提取公共测试工具
- 统一团队规范
