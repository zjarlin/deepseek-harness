# microkernel-admin：本 fork 的二开边界

本 fork（`zjarlin/deepseek-harness`）用于**上游追踪**与**布局补丁回灌**，
二开产物在独立仓库：<https://github.com/zjarlin/microkernel-admin>（MIT 兼容，署名见下）。

## 为什么不在本 fork 里做后台

dsh 是 React + Cordis 的会话型 harness，`packages/` 下 80+ 包围绕 session / llm / host 展开。
目标产品是一个 Vue 3 + 自研微内核的业务后台（若依式 CRUD + 组态画布），
需要的只是「三栏几何 + 面板导航」，不需要 harness 的会话语义。整仓分叉会得到两套 DI、
两套面板模型和一套用不上的会话运行时。

## 取用清单（只取布局，只保留一份）

| 本 fork | 下游 `@addzero/app-frame` |
| --- | --- |
| `packages/client/ui-layout/src/client/columns.ts` | `packages/app-frame/src/columns.ts`（纯函数逐条移植：`CENTER_MIN` / `SIDEBAR_MIN|MAX|DEFAULT|COLLAPSED|AUTO_COLLAPSE` / `RIGHTBAR_MIN` / `RIGHTBAR_MAX_RATIO` / `clampWidth` / `computeColumns` / `expandSidebarWidth`） |
| `packages/client/ui-layout/src/client/service.ts`（`ctx.layout`） | `packages/app-frame/src/layout.ts`（`createLayout()`：`selectPanel` / `beginNavigation` / `toggleSidebar` / `openRightbar` / `closeRightbar`，另加 `registerPanel` / `reset`） |
| `packages/client/ui-layout/src/client/AppFrame.tsx` | `packages/app-frame/src/AppFrame.vue`（三栏 + 拖拽手柄 + 窄屏自动收起 + `Ctrl/⌘+B` + 右栏全屏） |

未移植：Cordis 插件上下文、session/llm/host、`client/ui-*` 其余组件。
下游用自己的微内核（插件注册 / 依赖拓扑 / 路由菜单 / 命令事件）替代 Cordis 的装配职责。

## 回灌规则

1. 先比 `columns.ts`（几何常量与求解顺序是行为契约），再比 `service.ts`（方法语义），最后比 `AppFrame.tsx`（交互细节）；
2. 下游的改动若只是「Vue 实现细节」不回灌；若是**几何或方法语义**变化（例如新增最小宽度、
   改右栏让位顺序），两边都要改，且先在 `microkernel-admin` 验证；
3. 上游合并后跑一次 `gh repo sync zjarlin/deepseek-harness`，本 fork 不留长期分叉分支。

## 署名

下游 `packages/app-frame/README.md` 与 `docs/UPSTREAM-DSH.md` 均标注来源与 MIT 归属，
并保留 `columns.ts` 内的来源注释。
