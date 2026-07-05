# [0062. 阈值控制](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0062.%20%E9%98%88%E5%80%BC%E6%8E%A7%E5%88%B6)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. thresholds 配置](#3-thresholds-配置)
- [4. 单独设置每个维度](#4-单独设置每个维度)
- [5. CI 失败条件](#5-ci-失败条件)
- [6. 按文件设置阈值](#6-按文件设置阈值)
- [7. 100% 覆盖率](#7-100-覆盖率)
- [8. 渐进式提高](#8-渐进式提高)
- [9. 阈值与 Git Hook](#9-阈值与-git-hook)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- thresholds 设置最低覆盖率
- 单独设置每个维度
- CI 失败条件
- 按文件设置阈值
- 100% 覆盖率
- 渐进式提高
- 与 Git Hook 配合

## 2. 评价

阈值控制是保障代码质量的重要机制。低于阈值时测试会失败，适合 CI 门禁。建议根据项目实际情况设置合理阈值，逐步提高。

## 3. thresholds 配置

`coverage.thresholds` 设置覆盖率的最低要求：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80,
      },
    },
  },
})
```

如果覆盖率低于阈值，测试会失败。

## 4. 单独设置每个维度

```typescript
thresholds: {
  lines: 90,       // 行覆盖率 90%
  functions: 80,   // 函数覆盖率 80%
  branches: 70,    // 分支覆盖率 70%
  statements: 85,  // 语句覆盖率 85%
}
```

## 5. CI 失败条件

在 CI 中，覆盖率低于阈值会导致构建失败：

```yaml
# GitHub Actions
- name: Run tests
  run: vitest run --coverage
# 如果覆盖率低于阈值，上面的命令会返回非零退出码
# CI 流程会自动失败
```

## 6. 按文件设置阈值

可以为不同目录设置不同的阈值：

```typescript
coverage: {
  thresholds: {
    'src/core/**': {
      lines: 95,
      functions: 95,
    },
    'src/utils/**': {
      lines: 80,
      functions: 80,
    },
  },
}
```

## 7. 100% 覆盖率

追求 100% 覆盖率：

```typescript
thresholds: {
  lines: 100,
  functions: 100,
  branches: 100,
  statements: 100,
}
```

## 8. 渐进式提高

可以先设置较低的阈值，逐步提高：

```typescript
// 初始阶段
thresholds: {
  lines: 60,
  functions: 60,
}

// 逐步提高到
thresholds: {
  lines: 80,
  functions: 80,
}
```

## 9. 阈值与 Git Hook

配合 Git Hook 在提交前检查覆盖率：

```json
{
  "scripts": {
    "test:coverage": "vitest run --coverage"
  },
  "lint-staged": {
    "*.ts": "vitest related --coverage"
  }
}
```

## 10. 注意事项

- 阈值设置过高可能导致开发效率下降
- 建议根据项目实际情况设置合理的阈值
- 新项目可以从较低阈值开始，逐步提高
