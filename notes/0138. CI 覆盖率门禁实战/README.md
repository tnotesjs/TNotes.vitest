# [0138. CI 覆盖率门禁实战](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0138.%20CI%20%E8%A6%86%E7%9B%96%E7%8E%87%E9%97%A8%E7%A6%81%E5%AE%9E%E6%88%98)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 配置阈值](#3-配置阈值)
- [4. CI 运行](#4-ci-运行)
- [5. 报告上传](#5-报告上传)
- [6. GitLab CI](#6-gitlab-ci)
- [7. 分级阈值](#7-分级阈值)
- [8. 渐进式提高](#8-渐进式提高)
- [9. 报告格式](#9-报告格式)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 配置阈值（thresholds）
- CI 运行（vitest run --coverage）
- 报告上传（Codecov）
- GitLab CI 集成
- 分级阈值（按目录）
- 渐进式提高
- 报告格式（lcov）

## 2. 评价

覆盖率门禁是保障代码质量的重要机制。建议从较低阈值开始，逐步提高。分级阈值可以对核心模块设置更高要求。

## 3. 配置阈值

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

## 4. CI 运行

```yaml
# GitHub Actions
- name: Run tests with coverage
  run: vitest run --coverage
```

如果覆盖率低于阈值，命令会返回非零退出码，CI 失败。

## 5. 报告上传

```yaml
- name: Upload coverage to Codecov
  uses: codecov/codecov-action@v3
  with:
    files: ./coverage/lcov.info
```

## 6. GitLab CI

```yaml
test:
  script:
    - pnpm vitest run --coverage
  coverage: '/Statements\s*:\s*(\d+\.?\d*)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        coverage.xml
```

## 7. 分级阈值

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

## 8. 渐进式提高

```json
{
  "scripts": {
    "test:coverage": "vitest run --coverage",
    "test:coverage:strict": "vitest run --coverage --coverage.thresholds.lines=90"
  }
}
```

## 9. 报告格式

```typescript
coverage: {
  reporter: ['text', 'html', 'lcov', 'json'],
  reportsDirectory: './coverage',
}
```

## 10. 注意事项

- 阈值设置应合理，不要过高
- 使用 lcov 格式便于 CI 集成
- 定期查看覆盖率报告，识别未覆盖的代码
