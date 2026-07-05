# [0074. 样式与资源处理](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0074.%20%E6%A0%B7%E5%BC%8F%E4%B8%8E%E8%B5%84%E6%BA%90%E5%A4%84%E7%90%86)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. CSS 处理](#3-css-处理)
- [4. CSS Modules](#4-css-modules)
- [5. 静态资源](#5-静态资源)
- [6. Vite 资源处理](#6-vite-资源处理)
- [7. Mock CSS](#7-mock-css)
- [8. 全局 CSS](#8-全局-css)
- [9. SVG 处理](#9-svg-处理)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- CSS 导入处理（默认忽略）
- CSS Modules
- 静态资源（图片/字体）
- Vite 资源处理
- Mock CSS
- 全局 CSS
- SVG 处理

## 2. 评价

测试中样式不会实际渲染，CSS 导入会被忽略。如果测试依赖样式计算（如 offsetWidth），需要使用 Browser Mode。

## 3. CSS 处理

Vitest 默认忽略 CSS 导入：

```typescript
// 导入 CSS 不会报错，但不会应用样式
import './Button.css'

test('component with CSS', () => {
  render(<Button />)
  // 样式不会生效，但组件可以正常渲染
})
```

## 4. CSS Modules

```typescript
// styles.module.css
.button { color: red; }

// 测试中访问类名
import styles from './styles.module.css'

test('CSS modules', () => {
  expect(styles.button).toBeDefined()
})
```

## 5. 静态资源

图片、字体等静态资源在测试中会被转换为路径字符串：

```typescript
import logo from './logo.png'

test('static asset', () => {
  expect(typeof logo).toBe('string')
  expect(logo).toContain('logo')
})
```

## 6. Vite 资源处理

Vitest 复用 Vite 的资源处理能力：

```typescript
// vite.config.ts
export default defineConfig({
  assetsInclude: ['**/*.gltf'],
})
```

## 7. Mock CSS

如果需要 mock CSS 模块：

```typescript
vi.mock('./styles.css', () => ({
  default: {},
}))
```

## 8. 全局 CSS

```typescript
// setup.ts
import './global.css' // 全局样式在 setup 文件中导入
```

## 9. SVG 处理

```typescript
// 导入 SVG 作为组件（需要 vite-plugin-svg-icons 等插件）
import Icon from './icon.svg'

test('SVG', () => {
  expect(Icon).toBeDefined()
})
```

## 10. 注意事项

- 测试中样式不会实际渲染
- 如果测试依赖样式计算（如 offsetWidth），需要使用 Browser Mode
- CSS Modules 的类名会被哈希化
