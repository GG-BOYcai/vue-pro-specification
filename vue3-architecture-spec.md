# Vue3 企业级前端基础框架架构规范

> 本文档为 AI 可读的架构规范，用于指导 AI 生成或重构符合企业级标准的 Vue3 项目骨架。
> 所有代码生成、项目初始化、功能开发均须严格遵循本文档。

---

## 1. 技术栈与核心依赖

| 类别 | 技术选型 | 版本要求 | 说明 |
|------|---------|---------|------|
| 核心框架 | Vue | 3.x | 必须使用 Composition API |
| 构建工具 | Vite | 5.x+ | 推荐使用 pnpm 作为包管理器 |
| 语言 | TypeScript | 5.x+ | 严格模式，禁止使用 any |
| 状态管理 | Pinia | 2.x+ | 配合 pinia-plugin-persistedstate 持久化 |
| 路由 | Vue Router | 4.x+ | 支持动态路由与路由守卫 |
| UI 组件库 | Element Plus | 最新稳定版 | 按需引入，配合 unplugin-vue-components |
| CSS 方案 | Sass + Tailwind CSS | - | Sass 处理全局样式变量，Tailwind 处理原子类 |
| HTTP 客户端 | Axios | 1.x+ | 二次封装，统一拦截 |
| 代码规范 | ESLint + Prettier | - | 必须集成，统一风格 |
| 提交规范 | Commitlint + Husky + lint-staged | - | 强制校验提交信息 |

---

## 2. 项目目录结构规范

```
src/
├── api/                    # 统一接口管理（按业务模块划分）
│   ├── modules/            # 各模块接口
│   │   ├── user.ts         # 用户模块接口
│   │   ├── system.ts       # 系统模块接口
│   │   └── ...
│   └── index.ts            # 统一导出
├── assets/                 # 静态资源
│   ├── images/             # 图片
│   ├── fonts/              # 字体
│   └── styles/             # 全局样式（已废弃，迁移至 styles/）
├── components/             # 全局公共组件
│   ├── SvgIcon/            # SVG 图标组件
│   ├── Pagination/         # 分页组件
│   └── ...
├── composables/            # 组合式函数 (Hooks)
│   ├── useLoading.ts       # Loading 状态管理
│   ├── useTable.ts         # 表格通用逻辑
│   └── ...
├── directives/             # 全局自定义指令
│   ├── permission.ts       # 权限指令 v-permission
│   ├── loading.ts          # 加载指令 v-loading
│   └── index.ts            # 统一注册
├── layout/                 # 布局组件
│   ├── components/         # 布局子组件
│   │   ├── Sidebar.vue     # 侧边栏
│   │   ├── Navbar.vue      # 顶部导航
│   │   ├── TagsView.vue    # 标签页
│   │   └── AppMain.vue     # 主内容区
│   └── index.vue           # 布局入口
├── plugins/                # 插件注册
│   ├── element-plus.ts     # Element Plus 按需引入配置
│   └── index.ts            # 统一注册
├── router/                 # 路由配置
│   ├── modules/            # 路由模块（按业务拆分）
│   │   ├── user.ts
│   │   └── ...
│   ├── guards.ts           # 路由守卫
│   ├── constantRoutes.ts   # 静态路由（登录页等）
│   └── index.ts            # 路由实例与动态路由加载
├── store/                  # 全局状态管理
│   ├── modules/            # 按模块拆分 Store
│   │   ├── user.ts         # 用户信息
│   │   ├── permission.ts   # 权限/路由
│   │   ├── app.ts          # 应用全局状态
│   │   └── ...
│   └── index.ts            # 统一导出
├── styles/                 # 全局样式配置
│   ├── variables.scss      # Sass 变量
│   ├── mixins.scss         # Sass 混入
│   ├── reset.scss          # 样式重置
│   ├── global.scss         # 全局样式
│   └── index.scss          # 统一入口
├── utils/                  # 通用工具函数
│   ├── request.ts          # Axios 二次封装
│   ├── auth.ts             # Token 存取
│   ├── storage.ts          # 本地存储封装
│   ├── validate.ts         # 通用校验函数
│   └── index.ts            # 统一导出
├── views/                  # 页面级组件（按模块划分）
│   ├── login/
│   ├── dashboard/
│   ├── system/
│   └── ...
├── types/                  # 全局 TypeScript 类型声明
│   ├── api.d.ts            # 接口响应类型
│   ├── store.d.ts          # Store 类型
│   ├── env.d.ts            # 环境变量类型
│   ├── global.d.ts         # 全局类型扩展
│   └── ...
├── App.vue                 # 根组件
└── main.ts                 # 应用入口
```

---

## 3. 代码规范与工程化

### 3.1 ESLint 配置规范

#### 方式一：Flat Config（推荐，Vite 5.x+）

```js
// eslint.config.js
import { defineConfigWithVueTs, vueTsConfigs } from '@vue/eslint-config-typescript'
import prettierConfig from '@vue/eslint-config-prettier'

export default defineConfigWithVueTs(
  {
    ignores: ['dist', 'node_modules', '*.d.ts', 'commitlint.config.*', 'auto-imports.d.ts', 'components.d.ts']
  },
  vueTsConfigs.recommended,
  {
    rules: {
      // TypeScript 规则
      '@typescript-eslint/no-explicit-any': 'error',
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
      '@typescript-eslint/explicit-function-return-type': 'off',
      '@typescript-eslint/no-non-null-assertion': 'off',
      '@typescript-eslint/no-empty-function': 'off',

      // Vue 规则
      'vue/multi-word-component-names': 'off',
      'vue/no-v-html': 'off',
      'vue/require-default-prop': 'off',
      'vue/component-api-style': ['error', ['script-setup']],

      // 通用规则
      'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
      'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
      'no-var': 'error',
      'prefer-const': 'warn',
      'eqeqeq': ['warn', 'always'],
      'curly': ['warn', 'multi-line'],
      'no-else-return': 'warn',
      'no-multi-spaces': 'warn',
      'no-multiple-empty-lines': ['warn', { max: 2 }],
      'semi': ['warn', 'never'],
      'arrow-spacing': 'warn',
      'space-infix-ops': 'warn',
      'space-before-blocks': 'warn',
      'max-params': ['warn', 4],
      'max-nested-callbacks': ['warn', 3]
    }
  },
  prettierConfig
)
```

#### 方式二：传统 .eslintrc.cjs 格式（兼容旧项目）

```js
// .eslintrc.cjs
/* eslint-env node */
require('@rushstack/eslint-patch/modern-module-resolution')

module.exports = {
  root: true,
  env: {
    browser: true,
    node: true,
    es2021: true
  },
  parser: 'vue-eslint-parser',
  extends: [
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/eslint-config-typescript',
    '@vue/eslint-config-prettier/skip-formatting',
    'plugin:prettier/recommended'
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    parser: '@typescript-eslint/parser',
    sourceType: 'module'
  },
  plugins: ['vue', '@typescript-eslint', 'prettier'],
  rules: {
    // TypeScript 规则
    '@typescript-eslint/no-explicit-any': 'error',
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    '@typescript-eslint/explicit-function-return-type': 'off',
    '@typescript-eslint/no-non-null-assertion': 'off',
    '@typescript-eslint/no-empty-function': 'off',
    '@typescript-eslint/no-use-before-define': 'off',
    '@typescript-eslint/ban-ts-comment': 'off',
    '@typescript-eslint/ban-types': 'off',
    '@typescript-eslint/no-var-requires': 'off',

    // Vue 规则
    'vue/multi-word-component-names': 'off',
    'vue/no-v-html': 'off',
    'vue/require-default-prop': 'off',

    // 通用规则
    'no-var': 'error',
    'prettier/prettier': 'error',
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-duplicate-case': 'warn',
    'no-empty': 'warn',
    'no-unreachable': 'warn',
    'curly': 'warn',
    'default-case': 'warn',
    'dot-notation': 'warn',
    'eqeqeq': 'warn',
    'no-else-return': 'warn',
    'no-empty-function': 'warn',
    'no-lone-blocks': 'warn',
    'no-multi-spaces': 'warn',
    'no-redeclare': 'warn',
    'no-return-assign': 'warn',
    'no-self-assign': 'warn',
    'no-self-compare': 'warn',
    'no-useless-catch': 'warn',
    'no-useless-return': 'warn',
    'no-multiple-empty-lines': 'warn',
    'semi': ['warn', 'never'],
    'arrow-spacing': 'warn',
    'space-infix-ops': 'warn',
    'space-before-blocks': 'warn',
    'max-params': ['warn', 4],
    'max-nested-callbacks': ['warn', 3],
    'prefer-const': 'warn',
    'no-irregular-whitespace': 'warn',
    'no-fallthrough': 'warn'
  },
  globals: {
    defineProps: 'readonly',
    defineEmits: 'readonly',
    defineExpose: 'readonly',
    withDefaults: 'readonly'
  }
}
```

#### .eslintignore

```
node_modules
dist
*.d.ts
auto-imports.d.ts
components.d.ts
commitlint.config.*
```

### 3.2 Prettier 配置规范

#### .prettierrc.json

```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "none",
  "printWidth": 100,
  "bracketSpacing": true,
  "arrowParens": "always",
  "endOfLine": "auto",
  "vueIndentScriptAndStyle": false,
  "overrides": [
    {
      "files": "*.json",
      "options": {
        "printWidth": 200
      }
    }
  ]
}
```

#### .prettierignore

```
node_modules
dist
*.d.ts
auto-imports.d.ts
components.d.ts
pnpm-lock.yaml
package-lock.json
yarn.lock
```

### 3.3 EditorConfig

```ini
# .editorconfig
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false
```

### 3.4 TypeScript 配置

```jsonc
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "isolatedModules": true,
    "moduleDetection": "force",
    "noEmit": true,
    "jsx": "preserve",

    /* Linting - 严格模式 */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,

    /* 路径别名 */
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    },

    /* 类型声明 */
    "types": ["vite/client"]
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "src/**/*.d.ts"
  ],
  "exclude": ["node_modules", "dist"]
}
```

```jsonc
// tsconfig.app.json（如 Vite 模板拆分了配置）
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "tsBuildInfoFile": "./node_modules/.tmp/tsconfig.app.tsbuildinfo"
  },
  "include": ["src/**/*", "src/**/*.vue"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts", "**/*.test.ts"]
}
```

### 3.5 Git 提交规范（Commitlint）

提交格式：`type(scope): description`

| type | 说明 |
|------|------|
| feat | 新增功能 |
| fix | 修复 Bug |
| docs | 文档变更 |
| style | 代码格式（不影响逻辑） |
| refactor | 代码重构 |
| perf | 性能优化 |
| test | 测试相关 |
| build | 构建或依赖变更 |
| ci | CI/CD 配置变更 |
| chore | 其他杂项 |
| revert | 回退提交 |
| del | 删除功能 |
| update | 更新功能 |

```js
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'docs', 'style', 'refactor', 'perf', 'test', 'build', 'ci', 'chore', 'revert', 'del', 'update']
    ],
    'subject-case': [0]
  }
}
```

### 3.6 Husky + lint-staged 配置

```jsonc
// package.json 相关配置
{
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc -b && vite build",
    "preview": "vite preview",
    "lint": "eslint . --fix",
    "format": "prettier --write \"src/**/*.{js,ts,vue,scss,json}\"",
    "typecheck": "vue-tsc --noEmit",
    "prepare": "husky"
  },
  "lint-staged": {
    "*.{js,ts,vue,jsx,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{scss,css,json,md}": [
      "prettier --write"
    ]
  }
}
```

#### Husky 钩子文件

```bash
# .husky/pre-commit
pnpm lint-staged
```

```bash
# .husky/commit-msg
npx --no -- commitlint --edit $1
```

### 3.7 .gitignore

```gitignore
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

# Dependencies
node_modules
.yarn/install-state.gz

# Build output
dist
dist-ssr
*.local

# Editor directories and files
.vscode/*
!.vscode/extensions.json
!.vscode/settings.json
.idea
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

# Environment files
.env.local
.env.*.local

# TypeScript cache
*.tsbuildinfo

# OS files
.DS_Store
Thumbs.db

# Auto-generated
auto-imports.d.ts
components.d.ts
```

### 3.8 VSCode 工作区配置

```jsonc
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "never"
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact",
    "vue"
  ],
  "typescript.tsdk": "node_modules/typescript/lib",
  "vue.server.hybridMode": true,
  "files.eol": "\n",
  "files.insertFinalNewline": true,
  "files.trimTrailingWhitespace": true
}
```

```jsonc
// .vscode/extensions.json（推荐安装的扩展）
{
  "recommendations": [
    "vue.volar",
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode",
    "vue.vscode-typescript-vue-plugin",
    "antfu.iconify",
    "mikestead.dotenv"
  ]
}
```

---

## 4. 核心封装规范

### 4.1 Axios 二次封装

```ts
// src/utils/request.ts
import axios, { type AxiosInstance, type AxiosRequestConfig, type AxiosResponse, type InternalAxiosRequestConfig } from 'axios'
import { ElMessage, ElMessageBox } from 'element-plus'
import { getToken, removeToken } from './auth'
import router from '@/router'

// 响应数据泛型接口
export interface ApiResponse<T = any> {
  code: number
  data: T
  message: string
}

// 创建实例
const service: AxiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 15000,
  headers: { 'Content-Type': 'application/json;charset=UTF-8' }
})

// 请求拦截器
service.interceptors.request.use(
  (config: InternalAxiosRequestConfig) => {
    const token = getToken()
    if (token) {
      config.headers.Authorization = `Bearer ${token}`
    }
    return config
  },
  (error) => Promise.reject(error)
)

// 响应拦截器
service.interceptors.response.use(
  (response: AxiosResponse<ApiResponse>) => {
    const { code, message } = response.data
    if (code === 200 || code === 0) {
      return response.data as any
    }
    // 处理业务错误
    ElMessage.error(message || '请求失败')
    return Promise.reject(new Error(message || '请求失败'))
  },
  (error) => {
    const { response } = error
    if (response) {
      switch (response.status) {
        case 401:
          ElMessageBox.confirm('登录已过期，请重新登录', '提示', {
            confirmButtonText: '重新登录',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            removeToken()
            router.push('/login')
          })
          break
        case 403:
          ElMessage.error('没有权限访问该资源')
          break
        case 404:
          ElMessage.error('请求的资源不存在')
          break
        case 500:
          ElMessage.error('服务器内部错误')
          break
        default:
          ElMessage.error(response.data?.message || '请求失败')
      }
    } else {
      ElMessage.error('网络异常，请检查网络连接')
    }
    return Promise.reject(error)
  }
)

// 封装请求方法
export function get<T>(url: string, params?: object, config?: AxiosRequestConfig): Promise<ApiResponse<T>> {
  return service.get(url, { params, ...config })
}

export function post<T>(url: string, data?: object, config?: AxiosRequestConfig): Promise<ApiResponse<T>> {
  return service.post(url, data, config)
}

export function put<T>(url: string, data?: object, config?: AxiosRequestConfig): Promise<ApiResponse<T>> {
  return service.put(url, data, config)
}

export function del<T>(url: string, config?: AxiosRequestConfig): Promise<ApiResponse<T>> {
  return service.delete(url, config)
}

export default service
```

### 4.2 API 模块化封装

```ts
// src/api/modules/user.ts
import { get, post } from '@/utils/request'
import type { LoginParams, LoginResult, UserInfo } from '@/types/api'

/** 用户登录 */
export function loginApi(data: LoginParams) {
  return post<LoginResult>('/auth/login', data)
}

/** 获取用户信息 */
export function getUserInfoApi() {
  return get<UserInfo>('/auth/userInfo')
}

/** 退出登录 */
export function logoutApi() {
  return post('/auth/logout')
}
```

```ts
// src/api/index.ts
export * from './modules/user'
// 按模块导出...
```

### 4.3 Pinia Store 规范

```ts
// src/store/modules/user.ts
import { defineStore } from 'pinia'
import { piniaPersist } from '@/plugins/piniaPersist'
import { loginApi, getUserInfoApi, logoutApi } from '@/api/modules/user'
import type { LoginParams, UserInfo } from '@/types/api'
import { getToken, setToken, removeToken } from '@/utils/auth'
import router from '@/router'

interface UserState {
  token: string
  userInfo: UserInfo | null
  roles: string[]
  permissions: string[]
}

export const useUserStore = defineStore('user', {
  state: (): UserState => ({
    token: getToken() || '',
    userInfo: null,
    roles: [],
    permissions: []
  }),

  getters: {
    isLoggedIn: (state) => !!state.token
  },

  actions: {
    /** 登录 */
    async login(params: LoginParams) {
      const { data } = await loginApi(params)
      this.token = data.token
      setToken(data.token)
    },

    /** 获取用户信息 */
    async getUserInfo() {
      const { data } = await getUserInfoApi()
      this.userInfo = data
      this.roles = data.roles
      this.permissions = data.permissions
      return data
    },

    /** 退出登录 */
    async logout() {
      try {
        await logoutApi()
      } finally {
        this.resetState()
        router.push('/login')
      }
    },

    /** 重置状态 */
    resetState() {
      this.token = ''
      this.userInfo = null
      this.roles = []
      this.permissions = []
      removeToken()
    }
  },

  persist: {
    enabled: true,
    strategies: [
      {
        key: 'user-store',
        storage: localStorage,
        paths: ['token']
      }
    ]
  }
})
```

### 4.4 路由与权限控制

```ts
// src/router/constantRoutes.ts
import type { RouteRecordRaw } from 'vue-router'

/** 静态路由 - 无需权限即可访问 */
export const constantRoutes: RouteRecordRaw[] = [
  {
    path: '/login',
    name: 'Login',
    component: () => import('@/views/login/index.vue'),
    meta: { title: '登录', hidden: true }
  },
  {
    path: '/404',
    name: 'NotFound',
    component: () => import('@/views/error/404.vue'),
    meta: { title: '404', hidden: true }
  },
  {
    path: '/403',
    name: 'Forbidden',
    component: () => import('@/views/error/403.vue'),
    meta: { title: '403', hidden: true }
  }
]
```

```ts
// src/router/guards.ts
import type { Router } from 'vue-router'
import { useUserStore } from '@/store/modules/user'
import { usePermissionStore } from '@/store/modules/permission'

const WHITE_LIST = ['/login', '/404', '/403']

export function setupRouterGuards(router: Router) {
  // 前置守卫
  router.beforeEach(async (to, _from, next) => {
    // 页面标题
    document.title = `${to.meta.title || ''} - ${import.meta.env.VITE_APP_TITLE}`

    const userStore = useUserStore()
    const permissionStore = usePermissionStore()

    if (userStore.token) {
      if (to.path === '/login') {
        next({ path: '/' })
      } else {
        // 已登录但未获取用户信息
        if (!userStore.userInfo) {
          try {
            await userStore.getUserInfo()
            // 动态添加路由
            const routes = await permissionStore.generateRoutes()
            routes.forEach((route) => router.addRoute(route))
            next({ ...to, replace: true })
          } catch {
            userStore.resetState()
            next(`/login?redirect=${to.path}`)
          }
        } else {
          next()
        }
      }
    } else {
      if (WHITE_LIST.includes(to.path)) {
        next()
      } else {
        next(`/login?redirect=${to.path}`)
      }
    }
  })
}
```

### 4.5 按钮级权限指令

```ts
// src/directives/permission.ts
import type { Directive, DirectiveBinding } from 'vue'
import { useUserStore } from '@/store/modules/user'

/**
 * v-permission 指令
 * 用法: v-permission="['system:user:add']"
 * 无权限时移除元素
 */
export const permissionDirective: Directive = {
  mounted(el: HTMLElement, binding: DirectiveBinding<string[]>) {
    const { value } = binding
    if (value && value.length > 0) {
      const userStore = useUserStore()
      const hasPermission = value.some((perm) => userStore.permissions.includes(perm))
      if (!hasPermission) {
        el.parentNode?.removeChild(el)
      }
    }
  }
}

export default permissionDirective
```

```ts
// src/directives/index.ts
import type { App } from 'vue'
import permissionDirective from './permission'

export function setupDirectives(app: App) {
  app.directive('permission', permissionDirective)
}
```

---

## 5. 环境变量规范

```
# .env                    # 所有环境共享
# .env.development        # 开发环境
# .env.staging            # 测试环境
# .env.production         # 生产环境
```

```bash
# .env.development
VITE_APP_TITLE=管理系统
VITE_API_BASE_URL=/api
VITE_APP_ENV=development

# .env.staging
VITE_APP_TITLE=管理系统
VITE_API_BASE_URL=https://staging-api.example.com
VITE_APP_ENV=staging

# .env.production
VITE_APP_TITLE=管理系统
VITE_API_BASE_URL=https://api.example.com
VITE_APP_ENV=production
```

```ts
// src/types/env.d.ts
/// <reference types="vite/client" />

interface ImportMetaEnv {
  readonly VITE_APP_TITLE: string
  readonly VITE_API_BASE_URL: string
  readonly VITE_APP_ENV: 'development' | 'staging' | 'production'
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```

---

## 6. Vite 构建配置规范

```ts
// vite.config.ts
import { defineConfig, loadEnv } from 'vite'
import vue from '@vitejs/plugin-vue'
import { resolve } from 'path'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
import viteCompression from 'vite-plugin-compression'

export default defineConfig(({ mode }) => {
  const env = loadEnv(mode, process.cwd())

  return {
    plugins: [
      vue(),
      // Element Plus 按需自动引入
      Components({
        resolvers: [ElementPlusResolver()]
      }),
      // Gzip 压缩
      viteCompression({
        verbose: true,
        disable: false,
        threshold: 10240,
        algorithm: 'gzip',
        ext: '.gz'
      })
    ],

    resolve: {
      alias: {
        '@': resolve(__dirname, 'src')
      }
    },

    css: {
      preprocessorOptions: {
        scss: {
          additionalData: `@use "@/styles/variables.scss" as *;`
        }
      }
    },

    server: {
      port: 3000,
      open: true,
      proxy: {
        '/api': {
          target: env.VITE_API_BASE_URL,
          changeOrigin: true,
          rewrite: (path) => path.replace(/^\/api/, '')
        }
      }
    },

    build: {
      target: 'es2015',
      outDir: 'dist',
      chunkSizeWarningLimit: 1500,
      rollupOptions: {
        output: {
          // 分包策略
          manualChunks: {
            vue: ['vue', 'vue-router', 'pinia'],
            elementPlus: ['element-plus'],
            echarts: ['echarts']
          },
          chunkFileNames: 'js/[name]-[hash].js',
          entryFileNames: 'js/[name]-[hash].js',
          assetFileNames: '[ext]/[name]-[hash].[ext]'
        }
      }
    }
  }
})
```

---

## 7. 组件编写规范

### 7.1 组件命名

- 组件文件名：PascalCase（如 `UserList.vue`、`SearchForm.vue`）
- 组件 name：与文件名一致
- 目录组织：每个组件独立目录，包含 `index.vue`

### 7.2 组件模板

```vue
<!-- 必须使用 <script setup> 语法糖 -->
<script setup lang="ts">
import { ref, onMounted } from 'vue'
import type { PropType } from 'vue'

// Props 定义 - 必须提供完整类型
interface Props {
  title: string
  data: Record<string, unknown>[]
  loading?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  loading: false
})

// Emits 定义 - 必须提供完整类型
const emit = defineEmits<{
  (e: 'update', value: string): void
  (e: 'delete', id: number): void
}>()

// 响应式状态
const visible = ref(false)

// 方法
function handleSubmit() {
  emit('update', 'value')
}

// 生命周期
onMounted(() => {
  // 初始化逻辑
})
</script>

<template>
  <div class="component-name">
    <!-- 模板内容 -->
  </div>
</template>

<style scoped lang="scss">
.component-name {
  // 组件样式
}
</style>
```

### 7.3 Composable 编写规范

```ts
// src/composables/useTable.ts
import { ref, reactive } from 'vue'
import type { ApiResponse } from '@/utils/request'

interface TableOptions<T> {
  fetchApi: (params: any) => Promise<ApiResponse<{ list: T[]; total: number }>>
  defaultPageSize?: number
}

export function useTable<T = any>(options: TableOptions<T>) {
  const { fetchApi, defaultPageSize = 10 } = options

  const loading = ref(false)
  const tableData = ref<T[]>([]) as Ref<T[]>
  const total = ref(0)
  const queryParams = reactive({
    page: 1,
    pageSize: defaultPageSize
  })

  async function fetchData() {
    loading.value = true
    try {
      const { data } = await fetchApi(queryParams)
      tableData.value = data.list
      total.value = data.total
    } finally {
      loading.value = false
    }
  }

  function handlePageChange(page: number) {
    queryParams.page = page
    fetchData()
  }

  function handleSizeChange(size: number) {
    queryParams.pageSize = size
    queryParams.page = 1
    fetchData()
  }

  function resetQuery() {
    queryParams.page = 1
    fetchData()
  }

  return {
    loading,
    tableData,
    total,
    queryParams,
    fetchData,
    handlePageChange,
    handleSizeChange,
    resetQuery
  }
}
```

---

## 8. TypeScript 类型规范

### 8.1 核心原则

- **禁止使用 `any`**，如确实无法确定类型，使用 `unknown` 并做类型守卫
- 所有 API 接口必须提供完整的 TS 接口声明
- Props 和 Emits 必须使用泛型定义，禁止运行时声明
- 导出的函数/类必须提供返回值类型

### 8.2 类型文件组织

```ts
// src/types/api.d.ts - 通用接口响应类型
export interface PageParams {
  page: number
  pageSize: number
}

export interface PageResult<T> {
  list: T[]
  total: number
}

// src/types/api/user.d.ts - 用户模块接口类型
export interface LoginParams {
  username: string
  password: string
  captcha?: string
}

export interface LoginResult {
  token: string
  expiresIn: number
}

export interface UserInfo {
  id: number
  username: string
  avatar: string
  roles: string[]
  permissions: string[]
}
```

---

## 9. 性能优化规范

| 优化项 | 实现方式 |
|--------|---------|
| 路由懒加载 | `component: () => import('@/views/xxx/index.vue')` |
| 组件按需加载 | `defineAsyncComponent(() => import('@/components/xxx'))` |
| 第三方库按需引入 | Element Plus 使用 `unplugin-vue-components` 自动按需引入 |
| Gzip 压缩 | `vite-plugin-compression` |
| 图片压缩 | `vite-plugin-imagemin` |
| 分包策略 | `rollupOptions.output.manualChunks` 拆分 vendor |
| 长列表虚拟滚动 | 使用 `vue-virtual-scroller` 或 Element Plus 虚拟表格 |
| 防抖节流 | 搜索输入、窗口 resize 等场景必须使用 |
| Keep-alive | 需要缓存的路由组件使用 `<keep-alive>` |

---

## 10. 项目初始化清单

AI 在生成新项目时，必须完成以下步骤：

1. **项目初始化**：`pnpm create vite <project-name> --template vue-ts`
2. **安装核心依赖**：`vue-router`、`pinia`、`pinia-plugin-persistedstate`、`axios`、`element-plus`
3. **安装开发依赖**：`sass`、`tailwindcss`、`@vue/eslint-config-typescript`、`@vue/eslint-config-prettier`、`husky`、`lint-staged`、`@commitlint/cli`、`@commitlint/config-conventional`、`unplugin-vue-components`、`vite-plugin-compression`
4. **创建目录结构**：按第 2 节规范创建所有目录
5. **配置 ESLint + Prettier**：按第 3.1、3.2 节规范配置
6. **配置 Husky + lint-staged + Commitlint**：按第 3.3、3.4 节规范配置
7. **创建环境变量文件**：`.env`、`.env.development`、`.env.staging`、`.env.production`
8. **封装 Axios**：按第 4.1 节规范实现
9. **配置路由与权限**：按第 4.4 节规范实现
10. **配置 Vite 构建**：按第 6 节规范配置
11. **编写类型声明**：按第 8 节规范创建 `types/` 目录下所有类型文件
12. **验证**：运行 `pnpm lint` 和 `pnpm build` 确保无错误

---

## 11. 禁止事项

- 禁止在业务代码中使用 `any` 类型
- 禁止直接使用 `axios` 而不经过 `src/utils/request.ts` 封装
- 禁止在组件中使用 Options API，必须使用 `<script setup>`
- 禁止硬编码接口地址，必须统一在 `src/api/` 下管理
- 禁止在组件中直接操作 `localStorage`，必须使用 `src/utils/storage.ts` 封装
- 禁止提交未经 ESLint 检查的代码
- 禁止使用不符合 Commitlint 规范的提交信息
- 禁止在 `main.ts` 中全量引入 Element Plus
