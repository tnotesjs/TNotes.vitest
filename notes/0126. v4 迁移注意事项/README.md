# [0126. v4 迁移注意事项](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0126.%20v4%20%E8%BF%81%E7%A7%BB%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 破坏性变更](#3-破坏性变更)
- [4. 配置变化](#4-配置变化)
- [5. 依赖版本](#5-依赖版本)
- [6. Browser Mode](#6-browser-mode)
- [7. Workspace](#7-workspace)
- [8. 迁移步骤](#8-迁移步骤)
- [9. 常见问题](#9-常见问题)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 破坏性变更（Node.js 18+）
- 配置变化（threads -> pool）
- 依赖版本更新
- Browser Mode 配置调整
- Workspace 变化
- 迁移步骤

## 2. 评价

- v4 的主要变化是配置选项调整和最低 Node.js 版本要求
- 建议先在分支测试，确认无问题后再合并

## 3. 破坏性变更

Vitest v4 的主要破坏性变更：

- 最低 Node.js 版本要求：18+
- 某些 API 行为变化
- 配置选项调整

## 4. 配置变化

```typescript
// v3
test: {
  threads: true,
}

// v4
test: {
  pool: 'threads',
}
```

## 5. 依赖版本

```bash
# 更新依赖
pnpm add -D vitest@4
pnpm add -D @vitest/coverage-v8@4
pnpm add -D @vitest/ui@4
```

## 6. Browser Mode

v4 中 Browser Mode 配置变化：

```typescript
// v4
test: {
  browser: {
    enabled: true,
    provider: 'playwright',
    instances: [{ browser: 'chromium' }],
  },
}
```

## 7. Workspace

```typescript
// v4 使用 defineWorkspace
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace(['./packages/*'])
```

## 8. 迁移步骤

1. 更新依赖版本
2. 检查配置文件
3. 运行测试验证
4. 修复不兼容的 API

## 9. 常见问题

- `threads` 选项改为 `pool`
- Browser Mode 配置调整
- 某些 Mock 行为变化

## 10. 注意事项

- 查看官方迁移指南
- 逐步更新，先在分支测试
- 关注 breaking changes 列表
