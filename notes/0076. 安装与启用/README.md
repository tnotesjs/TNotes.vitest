# [0076. 安装与启用](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0076.%20%E5%AE%89%E8%A3%85%E4%B8%8E%E5%90%AF%E7%94%A8)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 安装依赖](#3-安装依赖)
- [4. 配置](#4-配置)
- [5. browser.enabled](#5-browserenabled)
- [6. provider 配置](#6-provider-配置)
- [7. instances 配置](#7-instances-配置)
- [8. headless 模式](#8-headless-模式)
- [9. 完整配置](#9-完整配置)
- [10. 验证安装](#10-验证安装)

<!-- endregion:toc -->

## 1. 本节内容

- 安装 @vitest/browser 和 playwright/webdriverio
- 配置 browser.enabled
- provider 配置
- instances 配置
- headless 模式
- 验证安装

## 2. 评价

Browser Mode 需要额外安装浏览器 provider。Playwright 是推荐选择，支持 Chromium、Firefox、WebKit 三种浏览器。

## 3. 安装依赖

```bash
# Playwright provider
pnpm add -D @vitest/browser @vitest/browser-playwright

# 或 WebDriverIO provider
pnpm add -D @vitest/browser @vitest/browser-webdriverio
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
      instances: [{ browser: 'chromium' }],
    },
  },
})
```

## 5. browser.enabled

`browser.enabled` 启用 Browser Mode：

```typescript
browser: {
  enabled: true,
}
```

可以在命令行中临时启用：

```bash
vitest --browser.enabled
```

## 6. provider 配置

```typescript
browser: {
  provider: 'playwright',
  // 或
  provider: 'webdriverio',
}
```

## 7. instances 配置

```typescript
browser: {
  instances: [
    { browser: 'chromium' },
    { browser: 'firefox' },
    { browser: 'webkit' },
  ],
}
```

## 8. headless 模式

```typescript
browser: {
  headless: true, // 无头模式，适合 CI
}
```

## 9. 完整配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    browser: {
      enabled: true,
      provider: 'playwright',
      headless: true,
      instances: [{ browser: 'chromium' }],
    },
  },
})
```

## 10. 验证安装

```bash
vitest run --browser.enabled
```

如果配置正确，测试会在浏览器中运行。
