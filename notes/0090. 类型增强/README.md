# [0090. 类型增强](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0090.%20%E7%B1%BB%E5%9E%8B%E5%A2%9E%E5%BC%BA)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 扩展 TestContext 类型](#3-扩展-testcontext-类型)
- [4. 使用扩展类型](#4-使用扩展类型)
- [5. 类型声明文件](#5-类型声明文件)
- [6. tsconfig 配置](#6-tsconfig-配置)
- [7. 接口合并](#7-接口合并)
- [8. 可选属性](#8-可选属性)
- [9. 泛型类型](#9-泛型类型)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 扩展 `TestContext` 类型
- 类型声明文件
- `tsconfig` 配置
- 接口合并
- 可选属性

## 2. 评价

TypeScript 项目需要扩展 `TestContext` 类型以获得编辑器支持。

## 3. 扩展 TestContext 类型

当通过 `beforeEach` 注入自定义属性时，需要扩展 TypeScript 类型：

```typescript
// vitest.d.ts
import 'vitest'

declare module 'vitest' {
  export interface TestContext {
    greeting?: string
    user?: { name: string }
    db?: Database
  }
}
```

## 4. 使用扩展类型

```typescript
beforeEach((context) => {
  context.greeting = 'hello' // TypeScript 识别
  context.user = { name: 'Alice' }
})

test('typed context', ({ greeting, user }) => {
  expect(greeting).toBe('hello') // 有类型提示
  expect(user?.name).toBe('Alice')
})
```

## 5. 类型声明文件

创建类型声明文件：

```typescript
// types/vitest.d.ts
import 'vitest'

declare module 'vitest' {
  interface TestContext {
    db: Database
    server: Server
    token: string
  }
}
```

## 6. tsconfig 配置

```json
{
  "compilerOptions": {
    "types": ["vitest/globals"]
  },
  "include": ["src/**/*.ts", "types/**/*.d.ts"]
}
```

## 7. 接口合并

TypeScript 的接口合并特性允许在多处扩展：

```typescript
// 文件 A
declare module 'vitest' {
  interface TestContext {
    dataA: string
  }
}

// 文件 B
declare module 'vitest' {
  interface TestContext {
    dataB: number
  }
}

// 最终 TestContext 包含 dataA 和 dataB
```

## 8. 可选属性

```typescript
declare module 'vitest' {
  interface TestContext {
    optionalData?: string
    requiredData: number
  }
}
```

## 9. 泛型类型

```typescript
declare module 'vitest' {
  interface TestContext<T = unknown> {
    data: T
  }
}
```

## 10. 注意事项

- 类型声明需要放在 `.d.ts` 文件中
- 确保 `tsconfig.json` 包含类型声明文件
- 类型扩展是全局的，影响所有测试文件
