# [0050. 全局状态隔离](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0050.%20%E5%85%A8%E5%B1%80%E7%8A%B6%E6%80%81%E9%9A%94%E7%A6%BB)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. isolate 配置](#3-isolate-配置)
- [4. Mock 清理](#4-mock-清理)
- [5. 环境重置](#5-环境重置)
- [6. 模块重置](#6-模块重置)
- [7. 完整隔离配置](#7-完整隔离配置)
- [8. 手动清理](#8-手动清理)
- [9. 性能与隔离的权衡](#9-性能与隔离的权衡)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- isolate 配置控制隔离
- Mock 清理（clearMocks/restoreMocks）
- 环境重置（unstubEnvs/unstubGlobals）
- 模块重置（unstubModules）
- 完整隔离配置

## 2. 评价

完整的隔离配置确保每个测试完全独立，避免状态泄漏。推荐配置 restoreMocks: true 自动恢复 Mock，平衡测试质量和性能。

## 3. isolate 配置

`test.isolate` 控制测试文件之间是否隔离：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    isolate: true, // 默认值
  },
})
```

`isolate: true` 时，每个测试文件在独立的上下文中运行，全局变量不会相互影响。

## 4. Mock 清理

配置自动清理 Mock 状态：

```typescript
test: {
  clearMocks: true,    // 每个测试前自动调用 mockClear
  restoreMocks: true,  // 每个测试后自动调用 mockRestore
  mockReset: true,     // 每个测试前自动调用 mockReset
}
```

推荐设置 `restoreMocks: true`，确保每个测试后 Mock 都被恢复。

## 5. 环境重置

`test.unstubEnvs` 和 `test.unstubGlobals` 控制环境变量和全局变量的重置：

```typescript
test: {
  unstubEnvs: true,    // 每个测试后恢复环境变量
  unstubGlobals: true, // 每个测试后恢复全局变量
}
```

## 6. 模块重置

`test.unstubModules` 控制模块 Mock 的重置：

```typescript
test: {
  unstubModules: true, // 每个测试后重置模块 Mock
}
```

## 7. 完整隔离配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    isolate: true,
    clearMocks: true,
    restoreMocks: true,
    mockReset: true,
    unstubEnvs: true,
    unstubGlobals: true,
    unstubModules: true,
  },
})
```

这是一套完整的隔离配置，确保每个测试完全独立。

## 8. 手动清理

也可以在测试中手动清理：

```typescript
import { vi, afterEach } from 'vitest'

afterEach(() => {
  vi.restoreAllMocks()
  vi.unstubAllEnvironments()
  vi.unstubAllGlobals()
  vi.resetModules()
})
```

## 9. 性能与隔离的权衡

完整的隔离配置会降低测试运行速度。可以根据项目需求调整：

- 小型项目：完整隔离，保证测试质量
- 大型项目：选择性隔离，优先性能
- CI 环境：完整隔离，确保可靠性
- 本地开发：部分隔离，优先速度

## 10. 注意事项

- `isolate: false` 可能导致测试间相互影响
- `restoreMocks: true` 是推荐配置，避免 Mock 泄漏
- 环境变量和全局变量的清理应在每个测试后执行
