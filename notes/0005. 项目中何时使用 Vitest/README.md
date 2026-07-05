# [0005. 项目中何时使用 Vitest](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0005.%20%E9%A1%B9%E7%9B%AE%E4%B8%AD%E4%BD%95%E6%97%B6%E4%BD%BF%E7%94%A8%20Vitest)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 前端项目](#3-前端项目)
- [4. Node 项目](#4-node-项目)
- [5. 库项目](#5-库项目)
- [6. Monorepo 项目](#6-monorepo-项目)
- [7. 组件库项目](#7-组件库项目)
- [8. 选择建议总结](#8-选择建议总结)

<!-- endregion:toc -->

## 1. 本节内容

- 前端项目：Vite 项目的首选测试框架，支持 Vue/React/Svelte 等
- Node 项目：可用于 CLI 工具、服务端中间件等纯 Node.js 项目
- 库项目：TypeScript 原生支持、快照测试、类型测试等保障 API 稳定性
- Monorepo 项目：Workspace 功能统一运行所有子包的测试
- 组件库项目：组件测试、快照测试、覆盖率报告等能力完善
- 选择建议总结：根据项目类型选择合适的测试框架

## 2. 评价

Vitest 适用于多种项目类型，尤其是 Vite 项目和前端项目。它的核心优势在于与 Vite 深度集成，复用相同的模块解析、插件系统和 TypeScript 处理逻辑。对于库项目和 Monorepo，Vitest 也提供了完善的支持。

## 3. 前端项目

Vitest 是前端项目的首选测试框架，尤其是使用 Vite 作为构建工具的项目。

适用的前端技术栈：

- Vue（通过 `@vitejs/plugin-vue`）
- React（通过 `@vitejs/plugin-react`）
- Svelte（通过 `@sveltejs/vite-plugin-svelte`）
- Solid（通过 `vite-plugin-solid`）
- Preact、Lit 等其他框架

前端项目通常需要测试 DOM 渲染、用户交互、组件状态等，Vitest 通过 jsdom/happy-dom 环境或 Browser Mode 提供完善的 DOM 测试能力。

## 4. Node 项目

Vitest 也可以用于纯 Node.js 项目，比如 CLI 工具、服务端中间件、BFF 层等。

```typescript
import { describe, it, expect } from 'vitest'
import { parseArgs } from '../src/cli'

describe('parseArgs', () => {
  it('解析 --name 参数', () => {
    const result = parseArgs(['--name', 'vitest'])
    expect(result.name).toBe('vitest')
  })

  it('缺少参数时抛出错误', () => {
    expect(() => parseArgs([])).toThrow('missing --name')
  })
})
```

对于纯 Node 项目，Jest 同样是成熟的选择。如果你的项目已经使用 Vite 或计划迁移到 Vite，选择 Vitest 可以保持工具链统一。

## 5. 库项目

开发 npm 库时，测试是保障 API 稳定性的关键。Vitest 对库项目有天然优势：

- TypeScript 原生支持，无需额外配置
- 快照测试，捕获 API 输出变化
- 覆盖率报告，确保代码质量
- 类型测试（`expectTypeOf`、`assertType`），验证类型定义正确性

```typescript
// 类型测试示例 - 验证库的类型导出
import { expectTypeOf } from 'vitest'
import { createStore } from 'my-state-lib'

expectTypeOf(createStore(0)).toHaveProperty('getState')
expectTypeOf(createStore(0)).toHaveProperty('setState')
expectTypeOf(createStore(0).getState).returns.toBeNumber()
```

## 6. Monorepo 项目

Vitest 的 Workspace 功能专为 Monorepo 设计，可以在根目录统一运行所有子包的测试。

```
my-monorepo/
├── vitest.workspace.ts
├── packages/
│   ├── core/          # Node 环境
│   ├── ui/            # jsdom 环境
│   └── utils/         # Node 环境
```

```typescript
// vitest.workspace.ts
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace([
  'packages/core',
  'packages/utils',
  {
    extends: true,
    test: {
      name: 'ui',
      root: './packages/ui',
      environment: 'jsdom',
    },
  },
])
```

每个子包可以有独立的 `vitest.config.ts`，Workspace 配置会合并根配置和子包配置。

## 7. 组件库项目

组件库是 Vitest 最适合的场景之一。它同时具备组件测试、快照测试、覆盖率报告等能力。

典型的组件库测试结构：

```
src/
├── Button/
│   ├── Button.vue
│   ├── Button.test.ts    # 单元/交互测试
│   └── __snapshots__/
├── Modal/
│   ├── Modal.vue
│   ├── Modal.test.ts
│   └── __snapshots__/
```

```typescript
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import Button from './Button.vue'

describe('Button', () => {
  it('渲染默认插槽内容', () => {
    const wrapper = mount(Button, { slots: { default: '提交' } })
    expect(wrapper.text()).toBe('提交')
  })

  it('disabled 状态下不可点击', async () => {
    const wrapper = mount(Button, { props: { disabled: true } })
    await wrapper.trigger('click')
    expect(wrapper.emitted('click')).toBeUndefined()
  })

  it('匹配快照', () => {
    const wrapper = mount(Button, { slots: { default: '提交' } })
    expect(wrapper.html()).toMatchSnapshot()
  })
})
```

## 8. 选择建议总结

| 项目类型         | 推荐 Vitest | 说明                     |
| ---------------- | ----------- | ------------------------ |
| Vite 前端项目    | 强烈推荐    | 零配置集成，性能最优     |
| Vue/React 组件库 | 强烈推荐    | 组件测试 + 快照 + 覆盖率 |
| Monorepo         | 推荐        | Workspace 原生支持       |
| 纯 Node 库       | 推荐        | TypeScript + 类型测试    |
| 纯 Node 服务     | 可选        | Jest 同样成熟            |
| Webpack 老项目   | 可选        | 迁移成本需评估           |
