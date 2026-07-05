# [0072. Svelte 与 Solid 框架](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0072.%20Svelte%20%E4%B8%8E%20Solid%20%E6%A1%86%E6%9E%B6)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. Svelte 测试](#3-svelte-测试)
- [4. Svelte 配置](#4-svelte-配置)
- [5. Solid 测试](#5-solid-测试)
- [6. Solid 配置](#6-solid-配置)
- [7. Preact 测试](#7-preact-测试)
- [8. Lit 测试](#8-lit-测试)
- [9. 框架选择](#9-框架选择)
- [10. 通用原则](#10-通用原则)

<!-- endregion:toc -->

## 1. 本节内容

- Svelte 测试（@testing-library/svelte）
- Solid 测试（@solidjs/testing-library）
- Preact 测试
- Lit 测试
- 框架选择
- 通用原则

## 2. 评价

不同框架有对应的测试库，但都遵循 Testing Library 的设计原则。选择测试库时应考虑框架特性和社区支持。

## 3. Svelte 测试

Svelte 组件测试使用 `@testing-library/svelte`：

```bash
pnpm add -D @testing-library/svelte jsdom
```

```typescript
import { render, screen } from '@testing-library/svelte'
import { test, expect } from 'vitest'
import Button from './Button.svelte'

test('renders button', () => {
  render(Button, { props: { label: 'Click' } })
  expect(screen.getByText('Click')).toBeInTheDocument()
})
```

## 4. Svelte 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import { svelte } from '@sveltejs/vite-plugin-svelte'

export default defineConfig({
  plugins: [svelte()],
  test: {
    environment: 'jsdom',
  },
})
```

## 5. Solid 测试

Solid 组件测试使用 `@solidjs/testing-library`：

```bash
pnpm add -D @solidjs/testing-library jsdom
```

```typescript
import { render, screen } from '@solidjs/testing-library'
import { test, expect } from 'vitest'
import Button from './Button'

test('renders button', () => {
  render(() => <Button label="Click" />)
  expect(screen.getByText('Click')).toBeInTheDocument()
})
```

## 6. Solid 配置

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import solid from 'vite-plugin-solid'

export default defineConfig({
  plugins: [solid()],
  test: {
    environment: 'jsdom',
  },
})
```

## 7. Preact 测试

```bash
pnpm add -D @testing-library/preact jsdom
```

```typescript
import { render, screen } from '@testing-library/preact'
import { test, expect } from 'vitest'
import Button from './Button'

test('renders button', () => {
  render(<Button label="Click" />)
  expect(screen.getByText('Click')).toBeInTheDocument()
})
```

## 8. Lit 测试

```bash
pnpm add -D @open-wc/testing jsdom
```

```typescript
import { fixture, html } from '@open-wc/testing'
import { test, expect } from 'vitest'
import './MyElement'

test('renders element', async () => {
  const el = await fixture(html`<my-element></my-element>`)
  expect(el.shadowRoot).toBeDefined()
})
```

## 9. 框架选择

| 框架   | 测试库                     | 环境  |
| ------ | -------------------------- | ----- |
| Vue    | `@vue/test-utils`          | jsdom |
| React  | `@testing-library/react`   | jsdom |
| Svelte | `@testing-library/svelte`  | jsdom |
| Solid  | `@solidjs/testing-library` | jsdom |

## 10. 通用原则

- 优先使用 Testing Library 系列
- 使用 jsdom 或 happy-dom 环境
- 测试用户可见行为，不测试实现细节
