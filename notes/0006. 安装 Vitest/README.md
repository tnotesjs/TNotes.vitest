# [0006. 安装 Vitest](https://github.com/tnotesjs/TNotes.vitest/tree/main/notes/0006.%20%E5%AE%89%E8%A3%85%20Vitest)

<!-- region:toc -->

- [1. 本节内容](#1-本节内容)
- [2. 评价](#2-评价)
- [3. npm](#3-npm)
- [4. pnpm](#4-pnpm)
- [5. yarn](#5-yarn)
- [6. bun](#6-bun)
- [7. 安装可选依赖](#7-安装可选依赖)
- [8. 验证安装](#8-验证安装)
- [9. 最小安装示例](#9-最小安装示例)

<!-- endregion:toc -->

## 1. 本节内容

- 四种包管理器安装方式：npm、pnpm、yarn、bun
- 可选依赖：jsdom、覆盖率 provider、UI、浏览器模式
- 验证安装方式
- 最小安装示例

## 2. 评价

Vitest 的安装非常简洁，一个命令即可完成。可选依赖按需安装，不会增加不必要的包体积。对于已有 Vite 项目，安装 Vitest 后几乎零配置即可开始编写测试。

## 3. npm

```bash
npm install -D vitest
```

## 4. pnpm

```bash
pnpm add -D vitest
```

## 5. yarn

```bash
yarn add -D vitest
```

## 6. bun

```bash
bun add -d vitest
```

## 7. 安装可选依赖

根据项目需求，可能还需要安装以下依赖：

```bash
# DOM 环境（测试组件时需要，二选一）
npm install -D jsdom
npm install -D happy-dom

# 覆盖率（可选，v8 或 istanbul 二选一）
npm install -D @vitest/coverage-v8
npm install -D @vitest/coverage-istanbul

# UI 界面（可选）
npm install -D @vitest/ui

# 浏览器模式（可选，推荐使用 npx vitest init browser 自动安装）
npm install -D @vitest/browser @vitest/browser-playwright
```

## 8. 验证安装

安装完成后，运行以下命令验证：

```bash
npx vitest --version
```

输出类似 `vitest/4.x.x` 即表示安装成功。

## 9. 最小安装示例

对于一个 TypeScript + Vite 的前端项目，最小安装命令为：

```bash
pnpm add -D vitest
```

只需要这一个依赖即可开始编写和运行测试。TypeScript 支持、ESM 支持等都由 Vitest 内置提供。Vite 的 `resolve.alias` 路径别名会被自动继承，但 `tsconfig.json` 中的 `paths` 别名需要额外安装 `vite-tsconfig-paths` 插件。
