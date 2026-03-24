---
description: Generate Electron scaffolding with an explicit interactive confirmation gate before initialization
argument-hint: [OPTIONS]
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, AskUserQuestion
---

# Electron Desktop App Scaffold Generator

## Usage

`/electron-scaffold [OPTIONS]`

### Parameters

- `[OPTIONS]`: Optional flags
  - `--name <PROJECT_NAME>`: Set project name (default: electron-app)
  - `--renderer <FRAMEWORK>`: Renderer framework (vue3 / react / vanilla; default: vue3)
  - `--ui-lib <LIBRARY>`: UI component library (element-plus / antd / none; default: element-plus)
  - `--updater`: Enable auto-update
  - `--skip-git`: Skip Git initialization
  - `--skip-install`: Skip dependency installation
  - `--minimal`: Minimal setup (core dependencies only)

## Context

- Generate a production-grade scaffold to quickly start an Electron desktop app
- Includes full configuration for main process and renderer process
- Supports hot reload, auto-update, packaging and release
- Follows Electron security best practices

## Your Role

You are the **Electron Application Architect**, responsible for generating a complete Electron project scaffold based on user requirements, including:

1. **Tech stack selection** – Decide the main-process and renderer-process technology combination
2. **Process architecture** – Design IPC and communication between main and renderer
3. **Config generation** – Generate configs for Electron, build tooling, and packaging tooling
4. **Core modules** – IPC, window management, auto-update, etc.
5. **Security setup** – Security options like contextIsolation and nodeIntegration

## Workflow

### Global Condition Controls (Important)

- `--renderer <vue3|react|vanilla>`: Branch dependencies/config/sample code by renderer.
- `--ui-lib <element-plus|antd|none>`: Install and configure only when supported by the chosen renderer (Vue=Element Plus, React=Ant Design).
- `--updater`: Generate and wire auto-update only when enabled; requires a valid `publish` config in `electron-builder`.
- `--skip-git`: Skip Git initialization and the first commit.
- `--skip-install`: Skip dependency installation (Phase 4).
- `--minimal`: Minimal mode; install only core runtime deps and minimal config. Skip UI libs, linter/formatter, Git hooks, updater, tray, and other optional modules.

---

### Phase 1: Parameter Parsing (Automatic)

**Goal**: Merge CLI args with defaults and decide whether to enter interactive completion.

**Steps**:
1. Parse CLI args; set defaults: `name=electron-app`, `renderer=vue3`, `ui-lib=element-plus`.
2. Generate a configuration draft: project metadata, renderer, UI library, feature toggles, packaging targets, etc.
3. If required fields are still missing (e.g., `appId`, `description`), enter Phase 2 and only ask for missing items; otherwise skip Phase 2.
4. Record global toggle states to drive conditional branches in later phases.

**Output**: A normalized configuration object for subsequent phases.

---

### 阶段2：需求确认（交互式）

**目标**：确认 Electron 项目配置细节（仅补齐缺失项；若参数已完整则自动跳过）

```
与用户确认以下信息：

1. **项目基本信息**：
   - 项目名称
   - 项目描述
   - 作者信息
   - 应用 ID（如：com.company.appname）

2. **渲染进程技术栈**：
   - 框架选择（Vue 3 / React）
   - UI 组件库（Element Plus / Ant Design）
   - 状态管理（Pinia / Zustand）
   - CSS预处理器（Sass / Less / Tailwind）
   - 国际化（i18n）
   - 请求库（Axios / Fetch）

3. **功能模块**：
   - 自动更新（electron-updater）
   - 系统托盘
   - 多窗口管理
   - 原生菜单
   - 快捷键绑定
   - 文件操作（读写、对话框）
   - 系统通知

4. **打包配置**：
   - 目标平台（Windows / macOS / Linux）
   - 安装程序类型（NSIS / DMG / AppImage）
   - 代码签名（是否需要）

5. **开发工具**：
   - TypeScript 严格模式
   - ESLint + Prettier
   - Git Hooks
   - 开发者工具（DevTools）

输出配置摘要并请求确认（若全部从 CLI 提供且校验通过，可直接进入阶段3）
```

**质量标准**：
- ✅ 所有必要信息已收集
- ✅ 技术栈组合兼容
- ✅ 安全配置符合最佳实践
- ✅ 用户已确认配置

### 阶段2.1：确认门（必须）

进入阶段3前，必须通过 `AskUserQuestion` 询问：`是否按当前配置继续初始化？(yes/no/modify)`

- `yes`：进入阶段3
- `modify`：回到阶段2更新配置后再次确认
- `no`：终止流程且不创建项目文件

---

### 阶段3：项目初始化（自动执行）

**目标**：创建 Electron 项目基础结构

#### 3.1 创建项目目录和基础文件

```bash
# 创建项目目录
mkdir <project-name>
cd <project-name>

# 初始化 package.json
npm init -y
```

#### 3.2 初始化 Git（除非指定 --skip-git）

```bash
git init
cat > .gitignore << EOL
node_modules
dist
dist-electron
release
.vscode/*
!.vscode/extensions.json
*.log
.DS_Store
.env.local
*.exe
*.dmg
*.AppImage
EOL
```

**检查点**：
- ✅ 项目目录已创建
- ✅ package.json 已初始化
- ✅ Git 仓库已初始化（如果需要）
- ℹ️ 建议在阶段5（配置文件生成）完成后再进行首次提交

---

### 阶段4：依赖安装（自动执行）

**目标**：安装 Electron 和渲染进程所需的依赖

**条件执行**：
- 若设置 `--skip-install`：跳过整个阶段。
- 若设置 `--minimal`：仅安装核心运行依赖（Electron、Vite、对应渲染器必须项），跳过 UI 库、ESLint/Prettier、Git Hooks、Updater 等。

#### 4.1 安装 Electron 核心依赖

```bash
# Electron 相关
npm install electron@latest
npm install -D electron-builder@latest

# 跨平台工具
npm install -D cross-env@latest

# 开发工具
npm install -D wait-on@latest concurrently@latest
```

#### 4.2 安装渲染进程依赖（按渲染器分支）

仅当未启用 `--minimal` 时才安装 UI 库与按需插件。

— Vue 3：

```bash
# Vue 3 核心
npm install vue@latest vue-router@latest pinia@latest

# 构建工具
npm install -D vite@latest @vitejs/plugin-vue@latest

# UI 组件库（如果选择）
## 当 ui-lib=element-plus 时
npm install element-plus@latest @element-plus/icons-vue@latest
npm install -D unplugin-vue-components@latest unplugin-auto-import@latest
```

— React：

```bash
# React 核心
npm install react@latest react-dom@latest

# 构建工具
npm install -D vite@latest @vitejs/plugin-react@latest

# UI 组件库（如果选择）
## 当 ui-lib=antd 时
npm install antd@latest
```

— Vanilla：

```bash
# 仅使用 Vite 提供的静态资源构建
npm install -D vite@latest
```

#### 4.3 安装 TypeScript 依赖

```bash
# TypeScript
npm install -D typescript@latest @types/node@latest @types/electron@latest
npm install -D vue-tsc  # Vue 项目需要

# Electron Vite 插件
npm install -D vite-plugin-electron@latest vite-plugin-electron-renderer@latest
```

#### 4.4 安装代码质量工具

```bash
# ESLint + Prettier
npm install -D eslint@latest prettier@latest eslint-config-prettier@latest eslint-plugin-prettier@latest
npm install -D @typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest
npm install -D eslint-plugin-vue@latest  # Vue 项目需要

# Git Hooks（可选）
npm install -D husky@latest lint-staged@latest @commitlint/cli@latest @commitlint/config-conventional@latest
```

#### 4.5 安装自动更新依赖（如果需要）

```bash
# 仅当启用 --updater 时
npm install electron-updater@latest
```

**检查点**：
- ✅ Electron 核心依赖安装成功
- ✅ 渲染进程依赖安装成功
- ✅ 开发工具依赖安装成功
- ✅ package.json 包含正确的依赖列表

#### 4.6 最简模式说明（当 --minimal）

- 保留：`electron`、`vite`、渲染器核心依赖（Vue/React 基础包或仅 Vite）、`vite-plugin-electron` 基础集成。
- 跳过：UI 组件库、`unplugin-*`、ESLint/Prettier、Git Hooks、`electron-updater`、系统托盘与菜单增强等可选项。
- 生成：最小的 `vite.config`、主进程与预加载脚本、单页渲染入口与简单示例。

---

### 阶段5：配置文件生成（自动执行）

**目标**：生成所有必要的配置文件

#### 5.0 占位符与项目元信息（自动执行）

生成文件前，替换以下占位符：
- `${PROJECT_NAME}`：项目名（来源：`--name`）。
- `${APP_ID}`：应用唯一 ID（来源：阶段0/参数）。
- `${PRODUCT_NAME}`：产品名；默认与项目名一致，可独立设置。
- `${PUBLISH_OWNER}` / `${PUBLISH_REPO}`：自动更新发布仓库信息（启用 `--updater` 时要求）。

替换范围：`package.json`、`electron-builder.json5`、`README.md`、示例代码与配置文件注释中涉及到的占位字段。

---

#### 5.1 Vite 配置（按渲染器分支）

— Vue 3 配置（`vite.config.ts`）
```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
import renderer from 'vite-plugin-electron-renderer'
import { resolve } from 'path'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    electron([
      {
        // 主进程入口文件
        entry: 'electron/main.ts',
        onstart(options) {
          // 启动 Electron 主进程
          options.startup(['electron', '.'])
        },
        vite: {
          build: {
            outDir: 'dist-electron',
            rollupOptions: {
              external: ['electron']
            }
          }
        }
      },
      {
        // 预加载脚本
        entry: 'electron/preload.ts',
        onstart(options) {
          // 通知渲染进程重新加载页面
          options.reload()
        },
        vite: {
          build: {
            outDir: 'dist-electron'
          }
        }
      }
    ]),
    renderer(),
    // 下方两个插件仅在 ui-lib=element-plus 时启用；若未选择 UI 库请删除
    AutoImport({
      resolvers: [ElementPlusResolver()],
      imports: ['vue', 'vue-router', 'pinia'],
      dts: 'src/types/auto-imports.d.ts',
    }),
    Components({
      resolvers: [ElementPlusResolver()],
      dts: 'src/types/components.d.ts',
    }),
  ],
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src'),
      '@electron': resolve(__dirname, 'electron'),
    },
  },
  server: {
    port: 5173,
  },
  build: {
    outDir: 'dist',
    emptyOutDir: true,
  },
})
```

— React 配置（`vite.config.ts`）
```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import electron from 'vite-plugin-electron'
import renderer from 'vite-plugin-electron-renderer'
import { resolve } from 'path'

export default defineConfig({
  plugins: [
    react(),
    electron([
      { entry: 'electron/main.ts', onstart: (o) => o.startup(['electron', '.']), vite: { build: { outDir: 'dist-electron', rollupOptions: { external: ['electron'] } } } },
      { entry: 'electron/preload.ts', onstart: (o) => o.reload(), vite: { build: { outDir: 'dist-electron' } } }
    ]),
    renderer()
  ],
  resolve: { alias: { '@': resolve(__dirname, 'src'), '@electron': resolve(__dirname, 'electron') } },
  server: { port: 5173 },
  build: { outDir: 'dist', emptyOutDir: true }
})
```

— Vanilla 配置（`vite.config.ts`）
```typescript
import { defineConfig } from 'vite'
import electron from 'vite-plugin-electron'
import renderer from 'vite-plugin-electron-renderer'
import { resolve } from 'path'

export default defineConfig({
  plugins: [
    electron([
      { entry: 'electron/main.ts', onstart: (o) => o.startup(['electron', '.']), vite: { build: { outDir: 'dist-electron', rollupOptions: { external: ['electron'] } } } },
      { entry: 'electron/preload.ts', onstart: (o) => o.reload(), vite: { build: { outDir: 'dist-electron' } } }
    ]),
    renderer()
  ],
  resolve: { alias: { '@': resolve(__dirname, 'src'), '@electron': resolve(__dirname, 'electron') } },
  server: { port: 5173 },
  build: { outDir: 'dist', emptyOutDir: true }
})
```

#### 5.2 TypeScript 配置

**tsconfig.json**
```json
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
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    /* Path Alias */
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@electron/*": ["electron/*"]
    }
  },
  "include": [
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "electron/**/*.ts"
  ],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

**tsconfig.node.json**
```json
{
  "compilerOptions": {
    "composite": true,
    "skipLibCheck": true,
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowSyntheticDefaultImports": true
  },
  "include": ["vite.config.ts", "electron/**/*.ts"]
}
```

#### 5.3 Electron Builder 配置

**electron-builder.json5**
```json5
{
  "appId": "${APP_ID}",
  "productName": "${PRODUCT_NAME}",
  "directories": {
    "output": "release/${version}"
  },
  "files": [
    "dist/**/*",
    "dist-electron/**/*",
    "package.json"
  ],
  "mac": {
    "category": "public.app-category.productivity",
    "target": ["dmg", "zip"],
    "icon": "build/icon.icns",
    "hardenedRuntime": true,
    "gatekeeperAssess": false,
    "entitlements": "build/entitlements.mac.plist",
    "entitlementsInherit": "build/entitlements.mac.plist"
  },
  "win": {
    "target": [
      {
        "target": "nsis",
        "arch": ["x64", "ia32"]
      }
    ],
    "icon": "build/icon.ico",
    "artifactName": "${productName}-${version}-Setup.${ext}"
  },
  "nsis": {
    "oneClick": false,
    "allowToChangeInstallationDirectory": true,
    "createDesktopShortcut": true,
    "createStartMenuShortcut": true,
    "shortcutName": "${productName}"
  },
  "linux": {
    "target": ["AppImage", "deb"],
    "icon": "build/icon.png",
    "category": "Utility"
  },
  "publish": {
    "provider": "github",
    "owner": "${PUBLISH_OWNER}",
    "repo": "${PUBLISH_REPO}"
  }
}
```

#### 5.4 ESLint 配置

**.eslintrc.cjs**
```javascript
module.exports = {
  root: true,
  env: {
    browser: true,
    es2021: true,
    node: true,
  },
  extends: [
    'eslint:recommended',
    'plugin:vue/vue3-recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:prettier/recommended',
  ],
  parser: 'vue-eslint-parser',
  parserOptions: {
    ecmaVersion: 'latest',
    parser: '@typescript-eslint/parser',
    sourceType: 'module',
  },
  plugins: ['vue', '@typescript-eslint', 'prettier'],
  rules: {
    'vue/multi-word-component-names': 'off',
    '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    '@typescript-eslint/no-explicit-any': 'warn',
    'prettier/prettier': 'error',
  },
  overrides: [
    {
      files: ['electron/**/*.ts'],
      rules: {
        '@typescript-eslint/no-var-requires': 'off',
      },
    },
  ],
}
```

#### 5.5 Prettier 配置

**.prettierrc.json**
```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "arrowParens": "always",
  "endOfLine": "auto"
}
```

#### 5.6 环境变量配置

**.env.development**
```env
VITE_DEV_SERVER_URL=http://localhost:5173
```

**.env.production**
```env
VITE_APP_TITLE=My Electron App
```

**检查点**：
- ✅ 所有配置文件已生成
- ✅ 配置文件语法正确
- ✅ Electron 安全配置启用
- ✅ 路径别名配置正确

#### 5.7 首次提交（自动执行）

完成阶段5全部文件生成后再提交，避免早期提交含大量空内容。

```bash
git add .
git commit -m "chore: scaffold: configs and structure"
```

若指定 `--skip-git`，跳过本步骤。

---

### 阶段6：项目结构搭建（自动执行）

**目标**：创建 Electron 标准化的目录结构

#### 6.1 创建目录结构

```
项目根目录/
├── electron/                   # Electron 主进程代码
│   ├── main.ts                # 主进程入口
│   ├── preload.ts             # 预加载脚本
│   ├── ipc/                   # IPC 通信模块
│   │   ├── index.ts
│   │   └── handlers/
│   │       ├── file.ts
│   │       └── system.ts
│   ├── windows/               # 窗口管理
│   │   ├── index.ts
│   │   └── MainWindow.ts
│   ├── menu/                  # 菜单配置
│   │   └── index.ts
│   ├── tray/                  # 系统托盘
│   │   └── index.ts
│   └── updater/               # 自动更新
│       └── index.ts
├── src/                       # 渲染进程代码（Vue 3）
│   ├── api/                   # API 模块
│   ├── assets/                # 静态资源
│   ├── components/            # 组件
│   ├── composables/           # 组合式函数
│   ├── router/                # 路由
│   ├── stores/                # 状态管理
│   ├── types/                 # 类型定义
│   │   ├── electron.d.ts      # Electron API 类型
│   │   ├── auto-imports.d.ts
│   │   └── components.d.ts
│   ├── utils/                 # 工具函数
│   ├── views/                 # 页面
│   ├── App.vue
│   ├── main.ts
│   └── style.css
├── build/                     # 构建资源
│   ├── icon.icns             # macOS 图标
│   ├── icon.ico              # Windows 图标
│   ├── icon.png              # Linux 图标
│   └── entitlements.mac.plist
├── public/                    # 公共资源
├── vite.config.ts
├── electron-builder.json5
├── tsconfig.json
├── tsconfig.node.json
├── package.json
└── README.md
```

**检查点**：
- ✅ 目录结构清晰
- ✅ 主进程和渲染进程分离
- ✅ IPC 通信模块独立

---

### 阶段7：核心模块实现（自动执行）

**目标**：生成 Electron 核心功能模块

#### 7.1 主进程入口

**electron/main.ts**
```typescript
import { app, BrowserWindow, ipcMain } from 'electron'
import { join } from 'path'
import { createMainWindow } from './windows'
import { setupIpcHandlers } from './ipc'
import { createMenu } from './menu'
import { createTray } from './tray'

// 禁用硬件加速（可选）
// app.disableHardwareAcceleration()

let mainWindow: BrowserWindow | null = null

// 开发环境 URL
const VITE_DEV_SERVER_URL = process.env.VITE_DEV_SERVER_URL

async function createWindow() {
  mainWindow = createMainWindow()

  // 加载页面
  if (VITE_DEV_SERVER_URL) {
    await mainWindow.loadURL(VITE_DEV_SERVER_URL)
    // 开发环境打开开发者工具
    mainWindow.webContents.openDevTools()
  } else {
    // 生产环境加载打包后的文件
    await mainWindow.loadFile(join(__dirname, '../dist/index.html'))
  }

  // 创建菜单
  createMenu()

  // 创建系统托盘（可选）
  // createTray(mainWindow)

  // 窗口关闭事件
  mainWindow.on('closed', () => {
    mainWindow = null
  })
}

// 应用准备就绪
app.whenReady().then(async () => {
  // 注册 IPC 处理器
  setupIpcHandlers()

  // 创建窗口
  await createWindow()

  // macOS 特性：点击 Dock 图标时重新创建窗口
  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) {
      createWindow()
    }
  })
})

// 所有窗口关闭时退出应用（macOS 除外）
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

// 退出前清理
app.on('before-quit', () => {
  // 清理操作
})

// 阻止导航到外部 URL（安全措施）
app.on('web-contents-created', (_, contents) => {
  contents.on('will-navigate', (event, navigationUrl) => {
    const url = new URL(navigationUrl)
    const isDev = Boolean(VITE_DEV_SERVER_URL)
    const allow = url.protocol === 'file:' || (isDev && url.origin === new URL(VITE_DEV_SERVER_URL as string).origin)
    if (!allow) event.preventDefault()
  })

  // 拦截 window.open 并限制外部链接
  contents.setWindowOpenHandler(({ url }) => {
    const target = new URL(url)
    const isDev = Boolean(VITE_DEV_SERVER_URL)
    const allow = target.protocol === 'file:' || (isDev && target.origin === new URL(VITE_DEV_SERVER_URL as string).origin)
    return allow ? { action: 'allow' } : { action: 'deny' }
  })
})
```

#### 7.2 预加载脚本（安全桥接）

**electron/preload.ts**
```typescript
import { contextBridge, ipcRenderer } from 'electron'

// 暴露安全的 API 给渲染进程
contextBridge.exposeInMainWorld('electronAPI', {
  // 系统信息
  platform: process.platform,
  
  // 文件操作
  selectFile: () => ipcRenderer.invoke('dialog:openFile'),
  saveFile: (content: string) => ipcRenderer.invoke('dialog:saveFile', content),
  readFile: (filePath: string) => ipcRenderer.invoke('file:read', filePath),
  writeFile: (filePath: string, content: string) =>
    ipcRenderer.invoke('file:write', filePath, content),

  // 窗口控制
  minimize: () => ipcRenderer.send('window:minimize'),
  maximize: () => ipcRenderer.send('window:maximize'),
  close: () => ipcRenderer.send('window:close'),
  isMaximized: () => ipcRenderer.invoke('window:isMaximized'),

  // 应用信息
  getVersion: () => ipcRenderer.invoke('app:getVersion'),
  
  // 通知
  showNotification: (title: string, body: string) =>
    ipcRenderer.send('notification:show', { title, body }),

  // 监听事件
  onUpdateAvailable: (callback: (info: any) => void) => {
    ipcRenderer.on('update:available', (_, info) => callback(info))
  },
  onUpdateDownloaded: (callback: () => void) => {
    ipcRenderer.on('update:downloaded', () => callback())
  },

  // 移除监听器
  removeAllListeners: (channel: string) => {
    ipcRenderer.removeAllListeners(channel)
  },
})

// TypeScript 类型定义
export type ElectronAPI = {
  platform: NodeJS.Platform
  selectFile: () => Promise<string | null>
  saveFile: (content: string) => Promise<string | null>
  readFile: (filePath: string) => Promise<string>
  writeFile: (filePath: string, content: string) => Promise<void>
  minimize: () => void
  maximize: () => void
  close: () => void
  isMaximized: () => Promise<boolean>
  getVersion: () => Promise<string>
  showNotification: (title: string, body: string) => void
  onUpdateAvailable: (callback: (info: any) => void) => void
  onUpdateDownloaded: (callback: () => void) => void
  removeAllListeners: (channel: string) => void
}
```

#### 7.3 窗口管理

**electron/windows/MainWindow.ts**
```typescript
import { BrowserWindow } from 'electron'
import { join } from 'path'

export function createMainWindow(): BrowserWindow {
  const win = new BrowserWindow({
    width: 1200,
    height: 800,
    minWidth: 800,
    minHeight: 600,
    show: false, // 先隐藏，加载完成后再显示
    frame: true, // 使用系统标题栏
    backgroundColor: '#ffffff',
    webPreferences: {
      preload: join(__dirname, '../preload.js'),
      // 安全设置
      nodeIntegration: false, // 禁用 Node.js 集成
      contextIsolation: true, // 启用上下文隔离
      sandbox: true, // 启用沙箱
      webSecurity: true,
    },
  })

  // 窗口准备好后再显示（避免白屏）
  win.once('ready-to-show', () => {
    win.show()
  })

  return win
}
```

**electron/windows/index.ts**
```typescript
export { createMainWindow } from './MainWindow'

// 可以添加其他窗口类型
// export { createSettingsWindow } from './SettingsWindow'
```

#### 7.4 IPC 通信处理器

**electron/ipc/index.ts**
```typescript
import { setupFileHandlers } from './handlers/file'
import { setupSystemHandlers } from './handlers/system'

export function setupIpcHandlers() {
  setupFileHandlers()
  setupSystemHandlers()
}
```

**electron/ipc/handlers/file.ts**
```typescript
import { ipcMain, dialog, BrowserWindow } from 'electron'
import { readFile, writeFile } from 'fs/promises'

export function setupFileHandlers() {
  // 打开文件对话框
  ipcMain.handle('dialog:openFile', async () => {
    const { canceled, filePaths } = await dialog.showOpenDialog({
      properties: ['openFile'],
      filters: [
        { name: 'Text Files', extensions: ['txt', 'md'] },
        { name: 'All Files', extensions: ['*'] },
      ],
    })

    if (canceled) {
      return null
    }
    return filePaths[0]
  })

  // 保存文件对话框
  ipcMain.handle('dialog:saveFile', async (_, content: string) => {
    const { canceled, filePath } = await dialog.showSaveDialog({
      filters: [
        { name: 'Text Files', extensions: ['txt'] },
        { name: 'All Files', extensions: ['*'] },
      ],
    })

    if (canceled || !filePath) {
      return null
    }

    await writeFile(filePath, content, 'utf-8')
    return filePath
  })

  // 读取文件
  ipcMain.handle('file:read', async (_, filePath: string) => {
    try {
      const content = await readFile(filePath, 'utf-8')
      return content
    } catch (error) {
      console.error('Failed to read file:', error)
      throw error
    }
  })

  // 写入文件
  ipcMain.handle('file:write', async (_, filePath: string, content: string) => {
    try {
      await writeFile(filePath, content, 'utf-8')
    } catch (error) {
      console.error('Failed to write file:', error)
      throw error
    }
  })
}
```

**electron/ipc/handlers/system.ts**
```typescript
import { ipcMain, BrowserWindow, app, Notification } from 'electron'

export function setupSystemHandlers() {
  // 窗口控制
  ipcMain.on('window:minimize', (event) => {
    const win = BrowserWindow.fromWebContents(event.sender)
    win?.minimize()
  })

  ipcMain.on('window:maximize', (event) => {
    const win = BrowserWindow.fromWebContents(event.sender)
    if (win?.isMaximized()) {
      win.unmaximize()
    } else {
      win?.maximize()
    }
  })

  ipcMain.on('window:close', (event) => {
    const win = BrowserWindow.fromWebContents(event.sender)
    win?.close()
  })

  ipcMain.handle('window:isMaximized', (event) => {
    const win = BrowserWindow.fromWebContents(event.sender)
    return win?.isMaximized() ?? false
  })

  // 应用信息
  ipcMain.handle('app:getVersion', () => {
    return app.getVersion()
  })

  // 系统通知
  ipcMain.on('notification:show', (_, { title, body }) => {
    new Notification({ title, body }).show()
  })
}
```

#### 7.5 菜单配置

**electron/menu/index.ts**
```typescript
import { Menu, app, shell } from 'electron'

export function createMenu() {
  const isMac = process.platform === 'darwin'

  const template: Electron.MenuItemConstructorOptions[] = [
    // macOS 应用菜单
    ...(isMac
      ? [
          {
            label: app.name,
            submenu: [
              { role: 'about' as const },
              { type: 'separator' as const },
              { role: 'services' as const },
              { type: 'separator' as const },
              { role: 'hide' as const },
              { role: 'hideOthers' as const },
              { role: 'unhide' as const },
              { type: 'separator' as const },
              { role: 'quit' as const },
            ],
          },
        ]
      : []),

    // 文件菜单
    {
      label: 'File',
      submenu: [isMac ? { role: 'close' as const } : { role: 'quit' as const }],
    },

    // 编辑菜单
    {
      label: 'Edit',
      submenu: [
        { role: 'undo' as const },
        { role: 'redo' as const },
        { type: 'separator' as const },
        { role: 'cut' as const },
        { role: 'copy' as const },
        { role: 'paste' as const },
        ...(isMac
          ? [
              { role: 'pasteAndMatchStyle' as const },
              { role: 'delete' as const },
              { role: 'selectAll' as const },
            ]
          : [{ role: 'delete' as const }, { type: 'separator' as const }, { role: 'selectAll' as const }]),
      ],
    },

    // 视图菜单
    {
      label: 'View',
      submenu: [
        { role: 'reload' as const },
        { role: 'forceReload' as const },
        { role: 'toggleDevTools' as const },
        { type: 'separator' as const },
        { role: 'resetZoom' as const },
        { role: 'zoomIn' as const },
        { role: 'zoomOut' as const },
        { type: 'separator' as const },
        { role: 'togglefullscreen' as const },
      ],
    },

    // 窗口菜单
    {
      label: 'Window',
      submenu: [
        { role: 'minimize' as const },
        { role: 'zoom' as const },
        ...(isMac
          ? [{ type: 'separator' as const }, { role: 'front' as const }]
          : [{ role: 'close' as const }]),
      ],
    },

    // 帮助菜单
    {
      role: 'help' as const,
      submenu: [
        {
          label: 'Learn More',
          click: async () => {
            await shell.openExternal('https://electronjs.org')
          },
        },
      ],
    },
  ]

  const menu = Menu.buildFromTemplate(template)
  Menu.setApplicationMenu(menu)
}
```

#### 7.6 系统托盘（可选）

**electron/tray/index.ts**
```typescript
import { Tray, Menu, nativeImage, BrowserWindow } from 'electron'
import { join } from 'path'

let tray: Tray | null = null

export function createTray(mainWindow: BrowserWindow) {
  // 创建托盘图标
  const icon = nativeImage.createFromPath(join(__dirname, '../../build/icon.png'))
  tray = new Tray(icon.resize({ width: 16, height: 16 }))

  // 托盘菜单
  const contextMenu = Menu.buildFromTemplate([
    {
      label: 'Show App',
      click: () => {
        mainWindow.show()
      },
    },
    {
      label: 'Quit',
      click: () => {
        mainWindow.destroy()
      },
    },
  ])

  tray.setToolTip('My Electron App')
  tray.setContextMenu(contextMenu)

  // 点击托盘图标显示窗口
  tray.on('click', () => {
    mainWindow.isVisible() ? mainWindow.hide() : mainWindow.show()
  })
}
```

#### 7.7 自动更新（可选）

**electron/updater/index.ts**
```typescript
import { autoUpdater } from 'electron-updater'
import { BrowserWindow } from 'electron'

export function setupAutoUpdater(mainWindow: BrowserWindow) {
  // 检查更新时不自动下载
  autoUpdater.autoDownload = false

  // 发现新版本
  autoUpdater.on('update-available', (info) => {
    mainWindow.webContents.send('update:available', info)
  })

  // 没有新版本
  autoUpdater.on('update-not-available', () => {
    console.log('App is up to date')
  })

  // 下载进度
  autoUpdater.on('download-progress', (progress) => {
    mainWindow.webContents.send('update:progress', progress)
  })

  // 下载完成
  autoUpdater.on('update-downloaded', () => {
    mainWindow.webContents.send('update:downloaded')
  })

  // 错误处理
  autoUpdater.on('error', (error) => {
    console.error('Update error:', error)
  })

  // 启动时检查更新（依赖 electron-builder 的 publish 配置）
  autoUpdater.checkForUpdatesAndNotify().catch((e) => console.error(e))
}
```

仅当传入 `--updater` 且 `electron-builder.json5` 已配置有效 `publish` 信息时启用。在主进程中以条件方式接线，例如：在 `app.whenReady()` 后判断开关再调用 `setupAutoUpdater(mainWindow)`。

#### 7.8 渲染进程入口

**src/main.ts**
```typescript
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import { createPinia } from 'pinia'
// 若未选择 Element Plus，请移除以下两行
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'

const app = createApp(App)
const pinia = createPinia()

app.use(router)
app.use(pinia)
// 若未选择 Element Plus，请移除此行
app.use(ElementPlus)

app.mount('#app')
```

#### 7.9 Electron API 类型定义

**src/types/electron.d.ts**
```typescript
import { ElectronAPI } from '@electron/preload'

declare global {
  interface Window {
    electronAPI: ElectronAPI
  }
}

export {}
```

#### 7.10 示例页面

**src/views/Home.vue**
```vue
<template>
  <div class="home">
    <h1>Electron + Vue 3 + TypeScript</h1>
    
    <div class="info-section">
      <el-card>
        <template #header>
          <span>系统信息</span>
        </template>
        <p>平台: {{ platform }}</p>
        <p>应用版本: {{ version }}</p>
      </el-card>
    </div>

    <div class="action-section">
      <el-button type="primary" @click="handleSelectFile">
        选择文件
      </el-button>
      <el-button type="success" @click="handleShowNotification">
        显示通知
      </el-button>
    </div>

    <div v-if="fileContent" class="file-content">
      <el-card>
        <template #header>
          <span>文件内容</span>
        </template>
        <pre>{{ fileContent }}</pre>
      </el-card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { ElMessage } from 'element-plus'

const platform = ref(window.electronAPI.platform)
const version = ref('')
const fileContent = ref('')

onMounted(async () => {
  version.value = await window.electronAPI.getVersion()
})

const handleSelectFile = async () => {
  try {
    const filePath = await window.electronAPI.selectFile()
    if (filePath) {
      fileContent.value = await window.electronAPI.readFile(filePath)
      ElMessage.success('文件读取成功')
    }
  } catch (error) {
    ElMessage.error('文件读取失败')
    console.error(error)
  }
}

const handleShowNotification = () => {
  window.electronAPI.showNotification('Hello', 'This is a notification from Electron!')
  ElMessage.success('通知已发送')
}
</script>

<style scoped lang="scss">
.home {
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;

  h1 {
    text-align: center;
    color: #409eff;
    margin-bottom: 30px;
  }

  .info-section {
    margin-bottom: 20px;
  }

  .action-section {
    margin-bottom: 20px;
    display: flex;
    gap: 10px;
  }

  .file-content {
    pre {
      max-height: 400px;
      overflow: auto;
      background: #f5f5f5;
      padding: 10px;
      border-radius: 4px;
    }
  }
}
</style>
```

**检查点**：
- ✅ 主进程和预加载脚本实现完整
- ✅ IPC 通信模块功能正常
- ✅ 窗口管理代码正确
- ✅ 安全配置符合最佳实践
- ✅ 示例页面可正常使用

---

### 阶段8：Package.json 配置（自动执行）

**目标**：配置 npm 脚本和项目元信息

根据渲染器选择脚本。以下为两套常用示例：

— Vue 3：
**package.json**
```json
{
  "name": "electron-app",
  "version": "1.0.0",
  "description": "Electron + Vue 3 + TypeScript Application",
  "main": "dist-electron/main.js",
  "author": "Your Name",
  "license": "MIT",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build && electron-builder",
    "build:win": "vue-tsc && vite build && electron-builder --win",
    "build:mac": "vue-tsc && vite build && electron-builder --mac",
    "build:linux": "vue-tsc && vite build && electron-builder --linux",
    "preview": "vite preview",
    "lint": "eslint . --ext .vue,.js,.ts,.tsx --fix",
    "format": "prettier --write .",
    "electron:dev": "vite",
    "electron:build": "vue-tsc && vite build"
  }
}
```

— React：
**package.json**
```json
{
  "name": "electron-app",
  "version": "1.0.0",
  "description": "Electron + React + TypeScript Application",
  "main": "dist-electron/main.js",
  "author": "Your Name",
  "license": "MIT",
  "scripts": {
    "dev": "vite",
    "build": "tsc -p tsconfig.json --noEmit && vite build && electron-builder",
    "build:win": "tsc -p tsconfig.json --noEmit && vite build && electron-builder --win",
    "build:mac": "tsc -p tsconfig.json --noEmit && vite build && electron-builder --mac",
    "build:linux": "tsc -p tsconfig.json --noEmit && vite build && electron-builder --linux",
    "preview": "vite preview",
    "lint": "eslint . --ext .jsx,.tsx,.js,.ts --fix",
    "format": "prettier --write .",
    "electron:dev": "vite",
    "electron:build": "tsc -p tsconfig.json --noEmit && vite build"
  }
}
```

**检查点**：
- ✅ 所有脚本配置正确
- ✅ main 字段指向主进程入口
- ✅ 项目元信息完整

---

### 阶段9：验证测试（自动执行）

**目标**：确保 Electron 应用可以正常运行

#### 9.1 TypeScript 类型检查

```bash
npx vue-tsc --noEmit
```

**预期结果**：
- ✅ 无类型错误
- ✅ Electron API 类型正确

#### 9.2 代码检查

```bash
npm run lint
```

**预期结果**：
- ✅ 无 ESLint 错误
- ✅ 代码风格一致

#### 9.3 开发模式测试

```bash
npm run dev
```

**预期结果**：
- ✅ Vite 开发服务器启动
- ✅ Electron 窗口正常打开
- ✅ 渲染进程页面正常显示
- ✅ IPC 通信正常工作
- ✅ 热重载功能正常

#### 9.4 生产构建测试

```bash
npm run build
```

**预期结果**：
- ✅ 渲染进程构建成功
- ✅ 主进程打包成功
- ✅ Electron Builder 打包成功
- ✅ 生成安装包（.exe/.dmg/.AppImage）

#### 9.5 失败处理策略

- **类型检查失败**：输出详细错误并中止；询问是否尝试自动降级严格性或继续后续阶段（默认中止）。
- **Lint 失败**：自动运行 `--fix`，若仍失败则报告并继续或中止由用户确认（默认继续）。
- **构建/打包失败**：中止流程并输出 `vite`/`electron-builder` 日志摘要与定位建议。
- **依赖安装失败**：提示代理/网络与镜像源配置建议；支持重试或跳过（默认重试一次）。

**质量门禁**：
```typescript
interface ValidationChecks {
  typescript_check: boolean;      // 必须: true
  lint_check: boolean;            // 必须: true
  dev_mode_works: boolean;        // 必须: true
  window_opens: boolean;          // 必须: true
  ipc_communication: boolean;     // 必须: true
  build_success: boolean;         // 必须: true
  security_enabled: boolean;      // 必须: true (contextIsolation, nodeIntegration=false)
}
```

---

### 阶段10：文档生成（自动执行）

**目标**：生成项目文档

#### 10.1 生成 README.md

```markdown
# ${PROJECT_NAME}

基于 Electron + Vue 3 + Vite + TypeScript 的跨平台桌面应用。

## 技术栈

### 主进程
- **Electron** - 跨平台桌面应用框架
- **TypeScript** - 类型安全

### 渲染进程
- **Vue 3** - 渐进式 JavaScript 框架
- **Vite** - 下一代构建工具
- **TypeScript** - JavaScript 超集
- **Vue Router** - 路由管理
- **Pinia** - 状态管理
- **Element Plus** - UI 组件库

### 构建工具
- **Electron Builder** - 应用打包
- **Vite Plugin Electron** - Vite + Electron 集成

## 快速开始

### 安装依赖
\`\`\`bash
npm install
\`\`\`

### 开发模式
\`\`\`bash
npm run dev
\`\`\`

### 构建应用

#### 构建所有平台
\`\`\`bash
npm run build
\`\`\`

#### 构建 Windows
\`\`\`bash
npm run build:win
\`\`\`

#### 构建 macOS
\`\`\`bash
npm run build:mac
\`\`\`

#### 构建 Linux
\`\`\`bash
npm run build:linux
\`\`\`

### 代码检查
\`\`\`bash
npm run lint
\`\`\`

### 代码格式化
\`\`\`bash
npm run format
\`\`\`

## 项目结构

\`\`\`
├── electron/           # Electron 主进程
│   ├── main.ts        # 主进程入口
│   ├── preload.ts     # 预加载脚本
│   ├── ipc/           # IPC 通信
│   ├── windows/       # 窗口管理
│   ├── menu/          # 菜单配置
│   └── tray/          # 系统托盘
├── src/               # Vue 3 渲染进程
│   ├── components/    # 组件
│   ├── views/         # 页面
│   ├── router/        # 路由
│   ├── stores/        # 状态管理
│   └── types/         # 类型定义
├── build/             # 构建资源（图标等）
└── public/            # 公共资源
\`\`\`

## IPC 通信

主进程和渲染进程通过 IPC 通信：

### 在渲染进程中调用
\`\`\`typescript
// 选择文件
const filePath = await window.electronAPI.selectFile()

// 读取文件
const content = await window.electronAPI.readFile(filePath)

// 显示通知
window.electronAPI.showNotification('Title', 'Body')
\`\`\`

### 添加新的 IPC 处理器

1. 在 \`electron/ipc/handlers/\` 添加处理器
2. 在 \`electron/preload.ts\` 暴露 API
3. 在 \`src/types/electron.d.ts\` 添加类型定义

## 安全最佳实践

- ✅ **contextIsolation**: 启用上下文隔离
- ✅ **nodeIntegration**: 禁用 Node.js 集成
- ✅ **sandbox**: 启用沙箱模式
- ✅ **preload**: 使用预加载脚本暴露安全 API
- ✅ **CSP**: 配置内容安全策略
- ✅ **Navigation**: 阻止导航到外部 URL

## 打包配置

打包配置在 \`electron-builder.json5\` 中：

- **Windows**: NSIS 安装程序
- **macOS**: DMG 磁盘映像
- **Linux**: AppImage / DEB

### 图标要求

- **Windows**: \`build/icon.ico\` (256x256)
- **macOS**: \`build/icon.icns\` (1024x1024)
- **Linux**: \`build/icon.png\` (512x512)

## 开发建议

- 主进程代码在 \`electron/\` 目录
- 渲染进程代码在 \`src/\` 目录
- 使用 TypeScript 严格模式
- 遵循 ESLint 规则
- 所有 IPC 通信必须通过预加载脚本
- 不要在渲染进程中直接使用 Node.js API

## 调试

### 主进程调试
在 VSCode 中添加调试配置：
\`\`\`json
{
  "type": "node",
  "request": "launch",
  "name": "Electron: Main",
  "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron",
  "program": "${workspaceFolder}/dist-electron/main.js"
}
\`\`\`

### 渲染进程调试
使用 Chrome DevTools（开发模式自动打开）

## 常见问题

### 1. 白屏问题
确保在 \`ready-to-show\` 事件后再显示窗口

### 2. IPC 通信失败
检查预加载脚本是否正确加载

### 3. 打包后无法运行
检查 \`package.json\` 的 \`main\` 字段是否正确

## 许可证

MIT
```

**检查点**：
- ✅ README.md 已生成
- ✅ 包含完整的使用说明
- ✅ 包含安全最佳实践

---

## 输出总结

完成所有阶段后，输出以下信息：

```
✅ Electron 应用脚手架生成成功！

📦 项目信息：
- 名称：${PROJECT_NAME}
- 类型：Electron Desktop App
- 位置：${PROJECT_PATH}
- 应用 ID：${APP_ID}

🛠️ 技术栈：
主进程：
  - Electron ${ELECTRON_VERSION}
  - TypeScript ${TS_VERSION}

渲染进程：
  - Vue 3 + Vite + TypeScript
  - ${UI_LIBRARY}
  - ${STATE_MANAGEMENT}

🚀 构建工具：
  - Electron Builder
  - Vite Plugin Electron

📝 后续步骤：
1. cd ${PROJECT_NAME}
2. npm install (如果未自动安装)
3. npm run dev

🔧 开发命令：
- npm run dev          # 开发模式
- npm run build        # 构建所有平台
- npm run build:win    # 构建 Windows
- npm run build:mac    # 构建 macOS
- npm run build:linux  # 构建 Linux

🔒 安全配置：
- ✅ Context Isolation 已启用
- ✅ Node Integration 已禁用
- ✅ Sandbox 模式已启用
- ✅ 预加载脚本正确配置

⚠️  重要提醒：
1. 在打包前配置图标文件（build/ 目录）
2. 更新 electron-builder.json5 中的应用信息
3. 配置代码签名（如需发布）
4. 测试所有目标平台的打包结果

📚 文档位置：
- README.md - 完整使用说明
- electron-builder.json5 - 打包配置
```

## 质量标准

### 必须满足
- ✅ Electron 安全配置正确（contextIsolation, nodeIntegration=false, sandbox）
- ✅ TypeScript strict 模式启用
- ✅ 主进程和渲染进程分离
- ✅ IPC 通信通过预加载脚本
- ✅ 开发模式可正常启动
- ✅ 生产构建成功
- ✅ 窗口管理正常
- ✅ 示例 IPC 功能可用

### 推荐满足
- ✅ 自动更新配置
- ✅ 系统托盘支持
- ✅ 原生菜单配置
- ✅ 文件操作示例
- ✅ 多平台打包配置
- ✅ DevTools 集成
- ✅ 图标文件准备

## 错误处理

如果在任何阶段遇到错误：
1. 根据阶段9.5的策略决定“自动修复/重试/中止/继续”。
2. 输出详细错误信息与定位建议（附相关日志片段）。
3. 当错误可绕过（如 Lint）时，允许在记录风险的前提下继续。
4. 支持在恢复后从最近成功阶段断点续跑。

## 扩展功能

### 可选模块

1. **自动更新**
   - electron-updater
   - GitHub Releases 集成

2. **系统集成**
   - 系统托盘
   - 全局快捷键
   - 系统通知
   - 开机自启动

3. **数据存储**
   - electron-store（配置存储）
   - SQLite（本地数据库）
   - IndexedDB（大数据存储）

4. **性能优化**
   - 懒加载窗口
   - 进程池管理
   - 内存优化

5. **安全增强**
   - CSP 配置
   - 证书固定
   - 加密存储

---
