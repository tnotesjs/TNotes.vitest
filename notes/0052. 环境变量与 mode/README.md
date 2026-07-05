# [0052. 环境变量与 mode](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0052.%20%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E4%B8%8E%20mode)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. mode 配置](#3-mode-配置)
- [4. .env 文件](#4-env-文件)
- [5. import.meta.env](#5-importmetaenv)
- [6. envPrefix](#6-envprefix)
- [7. process.env](#7-processenv)
- [8. 配置中的环境变量](#8-配置中的环境变量)
- [9. vi.stubEnv](#9-vistubenv)
- [10. 注意事项](#10-注意事项)

<!-- endregion:toc -->

## 1. 本节内容

- mode 配置影响 import.meta.env.MODE
- .env 文件加载
- import.meta.env 访问
- envPrefix 自定义前缀
- process.env
- vi.stubEnv 临时修改

## 2. 评价

Vitest 会自动加载 .env 文件，以 VITE\_ 为前缀的变量暴露到 import.meta.env。vi.stubEnv 用于测试中临时修改环境变量，测试结束后自动恢复。

## 3. mode 配置

`test.mode` 设置测试的模式，影响 `import.meta.env.MODE` 的值：

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    mode: 'test', // 默认值
  },
})
```

命令行指定：

```bash
vitest --mode production
```

## 4. .env 文件

Vitest 会自动加载 `.env` 文件：

```
.env                # 所有模式
.env.local          # 所有模式，本地覆盖
.env.test           # test 模式
.env.test.local     # test 模式，本地覆盖
```

环境变量的加载优先级：

1. `.env.test.local`
2. `.env.test`
3. `.env.local`
4. `.env`

## 5. import.meta.env

以 `VITE_` 为前缀的环境变量会暴露到 `import.meta.env`：

```typescript
// .env.test
VITE_API_URL=http://localhost:3000
VITE_APP_TITLE=Test App
```

```typescript
// 测试中访问
test('env vars', () => {
  expect(import.meta.env.VITE_API_URL).toBe('http://localhost:3000')
  expect(import.meta.env.VITE_APP_TITLE).toBe('Test App')
})
```

## 6. envPrefix

自定义环境变量前缀：

```typescript
// vite.config.ts
export default defineConfig({
  envPrefix: ['VITE_', 'MY_'],
})
```

## 7. process.env

也可以通过 `process.env` 访问环境变量：

```typescript
test('process.env', () => {
  expect(process.env.NODE_ENV).toBeDefined()
})
```

## 8. 配置中的环境变量

```typescript
test: {
  env: {
    NODE_ENV: 'test',
    VITE_API_URL: 'http://localhost:3000',
  },
}
```

配置中的 `env` 会覆盖 `.env` 文件中的值。

## 9. vi.stubEnv

在测试中动态修改环境变量：

```typescript
test('stub env', () => {
  vi.stubEnv('NODE_ENV', 'production')
  expect(process.env.NODE_ENV).toBe('production')
})
```

## 10. 注意事项

- `import.meta.env` 只暴露 `VITE_` 前缀的变量
- `process.env` 可以访问所有环境变量
- 测试中的环境变量修改不会影响其他测试（使用 `vi.stubEnv` 时）
