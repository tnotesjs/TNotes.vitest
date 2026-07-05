# [0077. Playwright 集成](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0077.%20Playwright%20%E9%9B%86%E6%88%90)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装](#3-安装)
- [4. 配置](#4-配置)
- [5. 支持的浏览器](#5-支持的浏览器)
- [6. 单浏览器测试](#6-单浏览器测试)
- [7. 多浏览器测试](#7-多浏览器测试)
- [8. Playwright 选项](#8-playwright-选项)
- [9. CI 配置](#9-ci-配置)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 playwright
- 配置 provider
- 支持的浏览器（chromium/firefox/webkit）
- 单浏览器测试
- 多浏览器测试
- Playwright 选项
- CI 配置

## 2. 评价

Playwright 是 Browser Mode 的推荐 provider，支持跨浏览器测试。CI 中需要安装浏览器二进制文件。

## 3. 安装

```bash
pnpm add -D @vitest/browser @vitest/browser-playwright
```

## 4. 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import { playwright } from '@vitest/browser-playwright'

export default defineConfig({
  test: {
    browser: {
      enabled: true,
      provider: playwright(),
      instances: [
        { browser: 'chromium' },
        { browser: 'firefox' },
        { browser: 'webkit' },
      ],
    },
  },
})
```

## 5. 支持的浏览器

| 浏览器     | 说明        |
| ---------- | ----------- |
| `chromium` | Chrome/Edge |
| `firefox`  | Firefox     |
| `webkit`   | Safari      |

## 6. 单浏览器测试

```typescript
browser: {
  instances: [
    { browser: 'chromium' },
  ],
}
```

## 7. 多浏览器测试

```typescript
browser: {
  instances: [
    { browser: 'chromium' },
    { browser: 'firefox' },
    { browser: 'webkit' },
  ],
}
```

## 8. Playwright 选项

```typescript
browser: {
  provider: 'playwright',
  providerOptions: {
    launch: {
      slowMo: 100,
      devtools: true,
    },
  },
}
```

## 9. CI 配置

```yaml
# GitHub Actions
- name: Install Playwright
  run: pnpm exec playwright install --with-deps

- name: Run tests
  run: vitest run --browser.enabled
```

## 10. 注意事项

- 首次运行需要安装浏览器二进制文件
- CI 中需要安装系统依赖
- WebKit 在 Linux 上可能需要额外配置
