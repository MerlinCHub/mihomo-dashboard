# Mihomo Dashboard 项目技术架构文档

## 项目概述

**项目名称**: Mihomo Dashboard  
**描述**: Clash/Mihomo dashboard for Steam OS - 一个为 Steam Deck 设计的 Decky Loader 插件  
**版本**: 0.0.1  
**作者**: MerlinCHub  
**许可证**: BSD-3-Clause

---

## 技术栈概览

### 前端技术栈
| 技术 | 版本 | 用途 |
|------|------|------|
| TypeScript | ^5.9.3 | 主要开发语言 |
| React | (由 @decky/ui 提供) | UI 框架 |
| @decky/ui | ^4.11.1 | Decky 前端 UI 组件库 |
| @decky/api | ^1.1.3 | Decky 插件 API |
| react-icons | ^5.5.0 | 图标库 |
| Rollup | ^4.53.3 | 打包工具 |
| @decky/rollup | ^1.0.2 | Decky 专用 Rollup 配置 |

### 后端技术栈
| 技术 | 用途 |
|------|------|
| Python 3 | 插件后端逻辑 (main.py) |
| decky-loader API | Decky 插件系统接口 |
| asyncio | Python 异步支持 |

### 开发工具
| 工具 | 用途 |
|------|------|
| pnpm (v9) | 包管理器 |
| Node.js (v16.14+) | JavaScript 运行时 |
| VS Code | 推荐 IDE |
| Docker | 后端二进制编译 |
| Git | 版本控制 |

---

## 目录结构详解

```
mihomo-dashboard/
├── 📄 main.py                  # Python 后端入口文件
├── 📄 package.json             # Node.js 项目配置
├── 📄 plugin.json              # Decky 插件元数据配置
├── 📄 tsconfig.json            # TypeScript 编译配置
├── 📄 rollup.config.js         # Rollup 打包配置
├── 📄 pnpm-lock.yaml           # pnpm 锁定文件
├── 📄 decky.pyi                # Decky Python API 类型提示
├── 📄 LICENSE                  # BSD-3-Clause 许可证
├── 📄 README.md                # 英文说明文档
├── 📄 README-zh.md             # 中文说明文档
├── 📁 src/                     # 前端源代码目录
├── 📁 assets/                  # 静态资源目录
├── 📁 defaults/                # 默认配置模板目录
├── 📁 py_modules/              # Python 模块依赖目录
├── 📁 .vscode/                 # VS Code 配置目录
└── 📁 node_modules/            # Node.js 依赖目录
```

---

## 核心文件说明

### 配置文件

#### `package.json`
- **作用**: Node.js 项目配置文件
- **内容**: 
  - 项目名称、版本、描述
  - 依赖包声明 (dependencies/devDependencies)
  - 构建脚本 (`build`, `watch`, `test`)
  - pnpm 配置

#### `plugin.json`
- **作用**: Decky 插件元数据
- **内容**:
  - `name`: 插件显示名称 "Mihomo Dashboard"
  - `author`: 作者信息
  - `flags`: 插件权限标志 (包含 `root` 权限)
  - `api_version`: API 版本
  - `publish`: 发布元数据 (标签、描述、图片)

#### `tsconfig.json`
- **作用**: TypeScript 编译配置
- **关键配置**:
  - 目标: ES2020
  - 模块系统: ESNext
  - JSX: react-jsx
  - 严格模式启用

#### `rollup.config.js`
- **作用**: 前端打包配置
- **使用**: @decky/rollup 预设配置

#### `decky.pyi`
- **作用**: Decky Python API 的类型提示文件
- **内容**: 
  - 环境变量常量定义
  - 迁移助手函数类型
  - 日志和事件 API 类型

---

## 源代码目录

### `src/` - 前端源码

#### `src/index.tsx`
- **作用**: 插件前端入口文件
- **功能**:
  - 定义插件 UI 组件 (`Content`)
  - 导出插件配置 (`definePlugin`)
  - 前后端通信 (callable API)
  - 事件监听 (addEventListener)

#### `src/types.d.ts`
- **作用**: TypeScript 类型声明
- **内容**: 静态资源模块声明 (SVG, PNG, JPG)

---

### `assets/` - 静态资源

#### `assets/logo.png`
- **作用**: 插件 Logo 图片

---

### `defaults/` - 默认配置

#### `defaults/defaults.txt`
- **作用**: 说明文档
- **用途**: 存放默认配置文件、主题模板等
- **注意**: 构建时会被打包到插件根目录

---

### `py_modules/` - Python 模块

- **作用**: 存放 Python 第三方依赖
- **当前状态**: 空目录 (仅有 `.keep` 占位文件)

---

### `.vscode/` - VS Code 配置

#### `.vscode/tasks.json`
- **作用**: VS Code 任务配置
- **主要任务**:
  - `setup`: 完整环境设置
  - `build`: 构建插件
  - `deploy`: 部署到 Steam Deck
  - `builddeploy`: 构建并部署

#### `.vscode/setup.sh`
- **作用**: 环境依赖安装脚本
- **功能**: 检查并安装 pnpm, Docker, Decky CLI

#### `.vscode/config.sh`
- **作用**: 配置检查脚本
- **功能**: 创建 settings.json 配置文件

#### `.vscode/build.sh`
- **作用**: 构建脚本
- **功能**: 调用 Decky CLI 构建插件

#### `.vscode/defsettings.json`
- **作用**: 默认 VS Code 设置模板

---

## Python 后端 API

### `main.py` 插件类结构

```python
class Plugin:
    # 生命周期方法
    async def _main(self)      # 插件加载时执行
    async def _unload(self)    # 插件卸载时执行
    async def _uninstall(self) # 插件删除时执行
    async def _migration(self) # 数据迁移
    
    # 可从前端调用的方法
    async def add(self, left, right)  # 示例：加法
    async def start_timer(self)       # 示例：启动计时器
    
    # 内部方法
    async def long_running(self)      # 长时间运行任务
```

### 前后端通信

**前端调用后端**:
```typescript
const add = callable<[first: number, second: number], number>("add");
const result = await add(1, 2);
```

**后端发送事件到前端**:
```python
await decky.emit("timer_event", "message", True, 123)
```

**前端监听事件**:
```typescript
addEventListener<[string, boolean, number]>("timer_event", (msg, flag, num) => {
    // 处理事件
});
```

---

## 构建流程

### 1. 环境准备
```bash
# 安装 pnpm (推荐 v9)
sudo npm i -g pnpm@9

# 安装依赖
pnpm i
```

### 2. 构建前端
```bash
pnpm run build
# 或使用 watch 模式
pnpm run watch
```

### 3. VS Code 任务
- **setup**: 完整环境初始化
- **build**: 构建插件
- **deploy**: 部署到 Steam Deck

---

## 部署配置

需要在 `.vscode/settings.json` 中配置以下参数：
- `deckip`: Steam Deck IP 地址
- `deckport`: SSH 端口
- `deckuser`: 用户名
- `deckpass`: 密码
- `deckdir`: Decky 安装目录
- `deckkey`: SSH 密钥 (可选)
- `pluginname`: 插件名称

---

## 项目特性

1. **Decky Loader 插件架构**
   - 支持 Steam Deck 游戏模式下运行
   - 前后端分离设计

2. **混合技术栈**
   - TypeScript/React 前端
   - Python 后端逻辑

3. **权限支持**
   - `root` 标志表示需要 root 权限运行

4. **自动化工具链**
   - VS Code 任务自动化
   - Docker 后端编译
   - pnpm 包管理

---

## 预期功能 (基于项目名推测)

作为 **Mihomo Dashboard**，该插件预计实现：

1. **Clash/Mihomo 代理管理**
   - 代理节点切换
   - 流量监控
   - 规则管理

2. **Steam Deck 集成**
   - 在游戏模式下快速切换代理
   - 状态指示器
   - 快捷操作面板

---

*文档生成时间: 2025年12月10日*
