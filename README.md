# Vue Pro Specification

**一套面向 AI 的企业级 Vue3 前端架构规范，一次配置，终身复用。**

---

## 为什么需要这个项目？

每个新项目启动时，你是否也在重复这些事：

- 从零搭建项目目录结构
- 重新配置 ESLint、Prettier、TypeScript
- 再封装一遍 Axios、Token、路由守卫
- 再写一遍权限控制、动态路由
- 再和同事争论代码风格和提交规范

**Vue Pro Specification** 把这些全部标准化为一份 AI 可读的规范文档。你只需要把它作为 Prompt 喂给 AI，就能在几分钟内生成一个符合企业级标准的项目骨架——不再重复造轮子。

---

## 核心卖点

### 1. AI-Ready，开箱即用

这不是一份给人"参考"的文档，而是一份**给 AI 执行的指令**。每个配置项都提供了完整的代码模板，AI 可以直接生成可运行的项目文件，无需二次修改。

### 2. 全链路覆盖

从目录结构到构建部署，从代码风格到提交规范，从接口封装到权限控制——**一个文档覆盖前端工程化的完整链路**：

```
技术栈选型 → 目录规范 → 代码规范 → 核心封装 → 环境配置 → 构建优化 → 性能规范 → 初始化清单
```

### 3. 企业级实战沉淀

所有规范均来自企业级项目实践，不是纸上谈兵：

- Axios 封装：401 自动跳转登录、403 无权限提示、500 服务异常、Token 自动携带
- 权限体系：路由守卫 + 动态路由 + 按钮级 `v-permission` 指令，三级权限全覆盖
- 状态持久化：Pinia + persistedstate，刷新不丢失登录态
- 接口类型安全：完整的 TypeScript 泛型封装，API 层全程类型推导

### 4. 双格式 ESLint 配置

同时提供 **Flat Config**（Vite 5.x+ 推荐）和**传统 .eslintrc.cjs** 两种格式，新项目老项目都能用。

### 5. 12 步初始化清单

AI 生成项目时的标准操作流程，确保每次产出的项目骨架一致、完整、可运行：

```
项目初始化 → 安装依赖 → 创建目录 → 配置规范 → 封装核心 → 验证构建
```

### 6. 8 条硬性红线

明确标注禁止事项，避免 AI 生成"能用但不规范"的代码：

- 禁止 `any` 类型
- 禁止裸用 Axios
- 禁止 Options API
- 禁止硬编码接口
- 禁止直接操作 localStorage
- ...

---

## 技术栈

| 类别 | 选型 |
|------|------|
| 框架 | Vue 3 + Composition API + `<script setup>` |
| 构建 | Vite 5.x+ |
| 语言 | TypeScript 5.x+（严格模式） |
| 状态管理 | Pinia + 持久化插件 |
| 路由 | Vue Router 4.x + 动态路由 |
| UI | Element Plus（按需引入） |
| 样式 | Sass + Tailwind CSS |
| 请求 | Axios 二次封装 |
| 规范 | ESLint + Prettier + Husky + Commitlint |

---

## 文档目录

| 文件 | 说明 |
|------|------|
| [vue3-architecture-spec.md](./vue3-architecture-spec.md) | Vue3 企业级前端架构规范（核心文档） |

### 架构规范包含

- 技术栈与核心依赖
- 项目目录结构规范
- 代码规范与工程化（ESLint / Prettier / EditorConfig / TypeScript / Commitlint / Husky / .gitignore / VSCode 配置）
- 核心封装规范（Axios / API 模块化 / Pinia Store / 路由守卫 / 权限指令）
- 环境变量规范（development / staging / production）
- Vite 构建配置（别名 / 代理 / Gzip / 分包）
- 组件编写规范（script setup 模板 / Composable 规范）
- TypeScript 类型规范
- 性能优化规范
- 项目初始化清单
- 禁止事项

### Git 指南包含

- 核心概念与文件状态流转
- 安装配置与 SSH 密钥
- 常用命令速查（仓库 / 日常 / 撤销 / 分支 / 远程 / 暂存 / 标签）
- 分支管理策略（Git Flow / GitHub Flow）
- 提交信息规范
- 合并与冲突解决
- 常见场景与解决方案
- 团队协作规范
- Git 别名配置

---

## 快速使用

### 方式一：作为 AI Prompt（推荐）

将 `vue3-architecture-spec.md` 的内容直接粘贴到 AI 对话中，然后告诉 AI：

```
请按照这份架构规范，为我生成一个完整的 Vue3 项目骨架
```

AI 会按照文档中的 12 步初始化清单，生成所有配置文件和核心代码。

### 方式二：作为团队规范

将本项目作为团队代码规范的参考文档，新成员入职时阅读，Code Review 时对照检查。

### 方式三：作为脚手架模板基础

基于本规范开发自己的 CLI 脚手架工具，一条命令生成标准项目。

---

## 适用场景

- 新项目从零启动，需要快速搭建标准骨架
- 老项目重构，需要引入规范化工程体系
- 团队协作，需要统一代码风格与提交规范
- AI 辅助开发，需要给 AI 提供明确的架构指令
- 技术培训，需要一份系统化的前端工程化参考

---

## 贡献

欢迎提交 Issue 和 Pull Request 来完善这份规范。

---

## License

MIT
