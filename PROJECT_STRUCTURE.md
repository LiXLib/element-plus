# Element Plus 工程目录结构说明

Element Plus 是一个基于 Vue 3 和 TypeScript 的开源组件库，本仓库采用 pnpm workspace 组织，是一个 monorepo 工程。根目录主要负责编排脚本、统一依赖版本和放置全局配置，组件源码、样式、文档、调试项目和内部构建工具分别放在不同的子目录中。

## 一、整体目录

```text
element-plus/
├─ packages/        # 组件库核心源码，各个 workspace 包
├─ docs/            # 官方文档站点，基于 VitePress
├─ play/            # 本地开发调试 playground
├─ internal/        # 构建、元数据、eslint 等内部工具包
├─ scripts/         # 仓库级脚本
├─ ssr-testing/     # SSR 相关测试
├─ breakings/       # breaking change 记录
├─ patches/         # pnpm patch 依赖补丁
├─ typings/         # 全局类型声明
└─ 根配置文件        # tsconfig、eslint、vitest、pnpm、commitlint 等
```

工作区配置主要由 `package.json` 和 `pnpm-workspace.yaml` 决定。当前 workspace 包括：

```yaml
packages:
  - packages/*
  - docs
  - play
  - internal/*
```

## 二、核心源码：packages

`packages/` 是组件库最核心的目录，Element Plus 的运行时代码、组件、主题样式、工具函数和国际化内容基本都在这里。

```text
packages/
├─ components/      # 每个 UI 组件的源码、测试、样式入口
├─ theme-chalk/     # Element Plus 的 SCSS 主题样式
├─ hooks/           # 共享组合式函数
├─ utils/           # 通用工具函数
├─ constants/       # 全局常量
├─ directives/      # Vue 指令
├─ locale/          # 国际化语言包
├─ test-utils/      # 测试辅助工具
└─ element-plus/    # 对外发布包入口，聚合组件和插件安装逻辑
```

### 1. packages/components

`packages/components/` 下每个组件通常都是一个独立目录。以 Button 组件为例：

```text
packages/components/button/
├─ index.ts         # 组件导出入口
├─ src/             # 组件实现源码
├─ style/           # 样式导入入口
└─ __tests__/       # 单元测试
```

Button 的源码目录示例：

```text
packages/components/button/src/
├─ button.vue       # Button 主组件
├─ button.ts        # props、emits、类型定义
├─ button-group.vue # ButtonGroup 组件
├─ button-group.ts  # ButtonGroup 类型/props
├─ use-button.ts    # 组件逻辑 hook
├─ constants.ts     # 组件内部常量
└─ instance.ts      # 实例类型
```

阅读或修改某个组件时，推荐顺序如下：

1. 先看 `packages/components/<component>/index.ts`，确认组件导出方式。
2. 再看 `src/<component>.vue`，理解模板、状态和事件。
3. 再看 `src/<component>.ts`，理解 props、emits 和类型定义。
4. 样式去 `packages/theme-chalk/src/` 或组件自身的 `style/` 目录查找。
5. 行为边界不明确时，看对应的 `__tests__/` 测试用例。

### 2. packages/theme-chalk

`packages/theme-chalk/` 是主题样式包，主要维护组件的 SCSS 样式、变量和主题构建逻辑。

```text
packages/theme-chalk/
├─ src/             # SCSS 源码
├─ buildfile.ts     # 样式构建入口
├─ package.json     # 样式包配置
└─ README.md
```

组件源码通常负责结构和交互逻辑，视觉样式集中在 `theme-chalk` 中维护。

### 3. packages/element-plus

`packages/element-plus/` 是最终对外发布的主包入口。它会聚合组件、指令、插件安装逻辑和类型导出，是使用者安装 `element-plus` 后实际消费的包入口之一。

### 4. 共享运行时包

以下目录提供跨组件复用能力：

- `packages/hooks/`：组合式函数，例如状态、事件、DOM 相关逻辑。
- `packages/utils/`：通用工具函数。
- `packages/constants/`：全局常量。
- `packages/directives/`：Vue 指令。
- `packages/locale/`：国际化语言包。
- `packages/test-utils/`：测试辅助方法。

## 三、文档站点：docs

`docs/` 是官方文档站点目录，基于 VitePress 构建。

```text
docs/
├─ .vitepress/      # VitePress 配置
├─ en-US/           # 英文文档内容
├─ examples/        # 组件示例代码
├─ public/          # 静态资源
├─ index.md         # 文档首页
├─ package.json     # 文档站脚本
└─ unocss.config.ts # 文档站样式工具配置
```

查看某个组件的文档和示例时，通常关注：

```text
docs/en-US/component/<component>.md
docs/examples/<component>/
```

如果组件新增了公开 API、事件、插槽或用法，一般也需要同步更新这里的文档。

## 四、本地调试：play

`play/` 是本地开发调试用的 playground。根目录的 `pnpm dev` 会执行 `pnpm -C play dev`，启动该调试项目。

```text
play/
├─ src/             # playground 页面源码
├─ main.ts          # 入口
├─ vite.config.mts  # Vite 配置
├─ index.html
└─ package.json
```

开发组件时，可以临时修改 `play/src/App.vue` 来验证组件效果。除非任务要求，调试用改动一般不应作为最终提交内容保留。

## 五、内部工具：internal

`internal/` 存放仓库内部工具包，主要服务于构建、元数据生成、lint 配置等流程。

```text
internal/
├─ build/           # 构建 Element Plus 的主流程
├─ build-constants/ # 构建常量
├─ build-utils/     # 构建辅助函数
├─ eslint-config/   # 仓库自定义 ESLint 配置
└─ metadata/        # 组件元数据生成/维护
```

根目录的 `pnpm build` 会走 `internal/build`。

## 六、测试相关目录

组件测试通常跟组件放在一起：

```text
packages/components/<component>/__tests__/
```

SSR 测试单独放在：

```text
ssr-testing/
```

常用测试和检查命令：

```bash
pnpm test
pnpm test packages/components/button/__tests__/button.test.tsx
pnpm typecheck
pnpm lint
```

## 七、根目录配置文件

根目录下的配置文件控制整个 monorepo 的依赖、构建、类型检查、测试和代码规范。

```text
package.json              # 根脚本、依赖、workspace 声明
pnpm-workspace.yaml       # pnpm workspace 配置、catalog 依赖版本
tsconfig*.json            # 不同场景的 TypeScript 配置
eslint.config.mjs         # ESLint 配置
vitest.config.mts         # Vitest 配置
commitlint.config.mjs     # 提交信息规范
pnpm-lock.yaml            # 锁定依赖版本
```

## 八、日常开发阅读路径

如果目标是修改一个组件，可以按下面路径定位：

1. 组件实现：`packages/components/<component>/src/`
2. 组件导出：`packages/components/<component>/index.ts`
3. 组件样式：`packages/theme-chalk/src/`
4. 组件样式入口：`packages/components/<component>/style/`
5. 组件测试：`packages/components/<component>/__tests__/`
6. 文档示例：`docs/examples/<component>/`
7. API 文档：`docs/en-US/component/<component>.md`
8. 本地调试：`play/src/App.vue`

简单来说：`packages/components` 写组件，`packages/theme-chalk` 写样式，`docs` 写文档和示例，`play` 做本地调试，`internal` 管构建工具，根目录负责统一工程配置和脚本。
