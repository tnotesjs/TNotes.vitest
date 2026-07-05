# [0075. Browser Mode 是什么](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0075.%20Browser%20Mode%20%E6%98%AF%E4%BB%80%E4%B9%88)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 什么是 Browser Mode](#3-什么是-browser-mode)
- [4. 与 jsdom 的区别](#4-与-jsdom-的区别)
- [5. 适用场景](#5-适用场景)
- [6. 优势](#6-优势)
- [7. 劣势](#7-劣势)
- [8. provider 选择](#8-provider-选择)
- [9. 浏览器选择](#9-浏览器选择)
- [10. 选择建议](#10-选择建议)

<!-- endregion:toc -->

## 1. 本节内容

- 在真实浏览器中运行测试
- 与 jsdom 的区别（API 完整性/布局渲染）
- 适用场景
- 优势（真实 DOM/浏览器 API）
- 劣势（速度慢/资源消耗）
- provider 选择

## 2. 评价

Browser Mode 使用真实浏览器环境，行为与生产环境一致。适合需要布局渲染和浏览器特有 API 的测试，但启动速度较慢。

## 3. 什么是 Browser Mode

Browser Mode 在真实的浏览器环境中运行测试，而非 jsdom 或 happy-dom 的模拟环境。

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

## 4. 与 jsdom 的区别

| 维度           | jsdom    | Browser Mode |
| -------------- | -------- | ------------ |
| 环境           | 模拟 DOM | 真实浏览器   |
| 性能           | 快       | 较慢         |
| API 完整性     | 部分     | 完整         |
| 布局渲染       | 不支持   | 支持         |
| 浏览器特有 API | 不支持   | 支持         |

## 5. 适用场景

- 需要真实 DOM 行为的测试
- 依赖浏览器特有 API 的代码
- 需要布局和渲染的组件测试
- 跨浏览器兼容性测试

## 6. 优势

- 使用真实的 DOM API
- 行为与生产环境一致
- 可以测试 Web Worker、WebGL 等
- 支持截图和可视化测试

## 7. 劣势

- 启动速度较慢
- 需要安装浏览器
- 资源消耗较大
- CI 环境配置复杂

## 8. provider 选择

Vitest 支持两种 Browser Mode provider：

- `playwright`：使用 Playwright 管理浏览器
- `webdriverio`：使用 WebDriverIO 管理浏览器

## 9. 浏览器选择

```typescript
browser: {
  instances: [
    { browser: 'chromium' },
    { browser: 'firefox' },
    { browser: 'webkit' },
  ],
}
```

## 10. 选择建议

- 大多数测试使用 jsdom/happy-dom
- 需要真实浏览器行为时使用 Browser Mode
- CI 中只测试 Chromium 即可
