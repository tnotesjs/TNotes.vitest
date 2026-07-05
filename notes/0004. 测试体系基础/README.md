# [0004. 测试体系基础](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0004.%20%E6%B5%8B%E8%AF%95%E4%BD%93%E7%B3%BB%E5%9F%BA%E7%A1%80)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. 测试体系概览](#3-测试体系概览)
- [4. 单元测试](#4-单元测试)
  - [4.1. 单元测试的特点](#41-单元测试的特点)
  - [4.2. 单元测试的边界](#42-单元测试的边界)
- [5. 集成测试](#5-集成测试)
  - [5.1. 集成测试的特点](#51-集成测试的特点)
  - [5.2. 集成测试的边界](#52-集成测试的边界)
- [6. 组件测试](#6-组件测试)
  - [6.1. 组件测试的特点](#61-组件测试的特点)
  - [6.2. 组件测试的边界](#62-组件测试的边界)
- [7. 端到端测试 (E2E)](#7-端到端测试-e2e)
  - [7.1. E2E 测试的特点](#71-e2e-测试的特点)
  - [7.2. E2E 测试的边界](#72-e2e-测试的边界)
- [8. 各层次对比](#8-各层次对比)
- [9. 测试金字塔](#9-测试金字塔)

<!-- endregion:toc -->

## 1. 本节内容

- 测试体系概览：从底层到顶层的测试层次结构
- 单元测试：针对最小可测试单元的验证
- 集成测试：验证多个模块或服务之间的协作
- 组件测试：验证 UI 组件的渲染输出和交互行为
- 端到端测试：从用户视角验证完整业务流程
- 各层次对比：不同测试层次的特点和适用场景
- 测试金字塔：指导测试分配比例的模型

## 2. 评价

理解不同测试层次的特点和边界，有助于合理规划测试策略。测试金字塔模型提供了指导原则，但实际项目应根据特点灵活调整。底层测试数量多、速度快、成本低；顶层测试数量少、速度慢、成本高。

## 3. 测试体系概览

软件测试通常分为多个层次，每个层次关注的范围和目标不同。理解各层次的边界有助于合理规划测试策略。

```
端到端测试 (E2E)
├── 组件测试 (Component)
│   ├── 集成测试 (Integration)
│   │   └── 单元测试 (Unit)
```

从底层到顶层，测试范围越来越大，执行速度越来越慢，维护成本也越来越高。

## 4. 单元测试

单元测试针对代码中最小的可测试单元（函数、方法、类）进行验证，不依赖外部资源。

```typescript
// 纯函数的单元测试
import { describe, it, expect } from 'vitest'

function formatPrice(cents: number): string {
  return `¥${(cents / 100).toFixed(2)}`
}

describe('formatPrice', () => {
  it('将分转换为元', () => {
    expect(formatPrice(1999)).toBe('¥19.99')
  })

  it('处理零值', () => {
    expect(formatPrice(0)).toBe('¥0.00')
  })
})
```

### 4.1. 单元测试的特点

- 速度快：毫秒级执行
- 隔离性强：不依赖数据库、网络、文件系统
- 确定性：相同输入总是相同输出
- 数量多：一个项目通常有成百上千个单元测试

### 4.2. 单元测试的边界

单元测试关注单个函数或模块的行为，不涉及多个模块之间的协作。如果测试需要真实调用另一个模块，就进入了集成测试的范畴。

## 5. 集成测试

集成测试验证多个模块或服务之间的协作是否正确。例如，测试一个函数调用了数据库查询模块，再对结果做处理。

```typescript
import { describe, it, expect, vi } from 'vitest'
import { getUserOrders } from './orders'
import * as db from './db'

// Mock 数据库模块，但保留模块间调用关系
vi.mock('./db', () => ({
  query: vi.fn(),
}))

describe('getUserOrders', () => {
  it('查询用户订单并按时间排序', async () => {
    vi.mocked(db.query).mockResolvedValue([
      { id: 2, date: '2024-01-02' },
      { id: 1, date: '2024-01-01' },
    ])

    const orders = await getUserOrders('user-1')

    expect(orders[0].id).toBe(2)
    expect(db.query).toHaveBeenCalledWith(expect.stringContaining('user-1'))
  })
})
```

### 5.1. 集成测试的特点

- 速度适中：比单元测试慢，但比 E2E 快
- 验证协作：关注模块之间的接口和数据流
- 可能需要 Mock：外部服务（数据库、API）通常需要模拟

### 5.2. 集成测试的边界

集成测试的范围是项目内部多个模块的协作，不涉及真实的用户界面或完整的部署环境。如果测试需要启动浏览器并模拟用户操作，就属于端到端测试。

## 6. 组件测试

组件测试是前端特有的测试层次，验证 UI 组件的渲染输出和交互行为。Vitest 配合 jsdom 或 happy-dom 环境，或使用 Browser Mode，都可以进行组件测试。

```typescript
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import Counter from './Counter.vue'

describe('Counter', () => {
  it('点击按钮后计数加一', async () => {
    const wrapper = mount(Counter)

    await wrapper.find('button').trigger('click')

    expect(wrapper.find('.count').text()).toBe('1')
  })
})
```

### 6.1. 组件测试的特点

- 关注用户可见行为：渲染结果、事件响应、状态变化
- 可以使用模拟 DOM（jsdom/happy-dom）或真实浏览器
- 通常需要框架特定的测试工具（Vue Test Utils、React Testing Library）

### 6.2. 组件测试的边界

组件测试介于单元测试和 E2E 测试之间。它测试单个组件或少量组件组合，不涉及页面跳转或完整的用户流程。当测试涉及多个页面的导航和完整业务流程时，就属于 E2E 测试。

## 7. 端到端测试 (E2E)

端到端测试从用户视角出发，在真实浏览器中模拟完整的用户操作流程，验证整个系统是否按预期工作。

E2E 测试通常使用 Playwright、Cypress 等工具，不在 Vitest 的核心职责范围内。但 Vitest 的 Browser Mode 可以覆盖部分接近 E2E 的场景。

```typescript
// 使用 Playwright 进行 E2E 测试（非 Vitest）
import { test, expect } from '@playwright/test'

test('用户登录流程', async ({ page }) => {
  await page.goto('/login')
  await page.fill('#username', 'admin')
  await page.fill('#password', '123456')
  await page.click('button[type="submit"]')
  await expect(page).toHaveURL('/dashboard')
})
```

### 7.1. E2E 测试的特点

- 速度最慢：需要启动真实浏览器和后端服务
- 覆盖面最广：端到端验证完整业务流程
- 维护成本最高：UI 变动容易导致测试失败

### 7.2. E2E 测试的边界

E2E 测试覆盖从用户入口到最终结果的完整路径，包括页面导航、表单提交、数据持久化等。它不关注内部实现细节，只关注用户可见的最终效果。

## 8. 各层次对比

| 维度     | 单元测试      | 集成测试      | 组件测试        | E2E 测试      |
| -------- | ------------- | ------------- | --------------- | ------------- |
| 测试范围 | 单个函数/模块 | 多模块协作    | UI 组件         | 完整用户流程  |
| 执行速度 | 极快          | 较快          | 较快            | 慢            |
| 外部依赖 | 无            | Mock 部分依赖 | 模拟 DOM/浏览器 | 真实环境      |
| 维护成本 | 低            | 中            | 中              | 高            |
| 适合工具 | Vitest        | Vitest        | Vitest          | Playwright 等 |
| 典型数量 | 大量          | 中等          | 中等            | 少量          |

## 9. 测试金字塔

测试金字塔是一种指导测试分配比例的模型：

```
        /  E2E  \          少量
       / 组件测试 \         适量
      / 集成测试   \        较多
     / 单元测试     \       大量
```

核心原则：底层测试数量最多、速度最快、成本最低；顶层测试数量最少、速度最慢、成本最高。

实际项目中不必严格遵循比例，应根据项目特点灵活调整。例如，纯函数库项目可以几乎全部使用单元测试；前端组件库项目则需要大量组件测试。
