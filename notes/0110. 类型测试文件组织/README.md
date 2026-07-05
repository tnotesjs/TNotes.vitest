# [0110. 类型测试文件组织](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0110.%20%E7%B1%BB%E5%9E%8B%E6%B5%8B%E8%AF%95%E6%96%87%E4%BB%B6%E7%BB%84%E7%BB%87)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. .test-d.ts 后缀](#3-test-dts-后缀)
- [4. 文件命名](#4-文件命名)
- [5. 配置 include](#5-配置-include)
- [6. 类型声明](#6-类型声明)
- [7. 与运行时测试分离](#7-与运行时测试分离)
- [8. 组织结构](#8-组织结构)
- [9. CI 集成](#9-ci-集成)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- .test-d.ts 后缀
- 文件命名规范
- 配置 include
- 类型声明
- 与运行时测试分离
- 组织结构
- CI 集成

## 2. 评价

类型测试文件使用 .test-d.ts 后缀与运行时测试分离。建议为重要的公开 API 添加类型测试，确保类型稳定性。

## 3. .test-d.ts 后缀

类型测试文件使用 `.test-d.ts` 后缀：

```
src/
├── utils.ts
├── utils.test.ts      # 运行时测试
└── utils.test-d.ts    # 类型测试
```

## 4. 文件命名

```typescript
// math.test-d.ts
import { expectTypeOf, assertType } from 'vitest'
import { add, multiply } from './math'

expectTypeOf(add).returns.toBeNumber()
assertType<number>(multiply(2, 3))
```

## 5. 配置 include

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    include: ['**/*.test.ts', '**/*.test-d.ts'],
  },
})
```

默认配置已经包含 `.test-d.ts` 文件。

## 6. 类型声明

```typescript
// types/vitest.d.ts
import 'vitest'

declare module 'vitest' {
  interface Assertion<T> {
    toBeValidDate(): void
  }
}
```

## 7. 与运行时测试分离

类型测试和运行时测试可以分开运行：

```bash
# 运行所有测试
vitest run

# 只运行类型测试
vitest run "src/**/*.test-d.ts"
```

## 8. 组织结构

```
src/
├── components/
│   ├── Button.tsx
│   ├── Button.test.ts      # 组件测试
│   └── Button.test-d.ts    # 类型测试
├── utils/
│   ├── math.ts
│   ├── math.test.ts
│   └── math.test-d.ts
```

## 9. CI 集成

类型测试在 CI 中与运行时测试一起运行：

```yaml
- name: Run tests
  run: vitest run
```

## 10. 注意事项

- 类型测试文件不会生成运行时代码
- 类型测试在 TypeScript 编译时检查
- 建议为重要的公开 API 添加类型测试
