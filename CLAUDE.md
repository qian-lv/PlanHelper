# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

PlanHelper — 个人计划管理工具，纯前端单页应用 (HTML + CSS + Vanilla JS)。数据存储在 localStorage 中，无后端依赖。

## Architecture

- **`index.html`** — 全部代码（样式 + 结构 + 逻辑），约 1466 行
- **数据模型**: `goals[]`（目标 → 阶段 → 任务）+ `days{}`（日计划）+ `routines[]`（日常惯例）
- **Store 模式**: `Store` 对象封装所有数据读写，`Store.init()` 从 localStorage 加载，`Store.save()` 持久化
- **渲染模式**: 每个页面有独立的 `renderXxx()` 函数，数据变更后调用 `renderAll()` 全量重渲染
- **页面路由**: 侧边栏 `data-page` 属性切换 `.page` 元素的显示/隐藏
- **拖拽**: 原生 HTML5 Drag & Drop API，用于任务池拖拽、画布卡片 reposition、纵向组合

### 数据结构

```json
{
  "version": 3,
  "goals": [{ "id": "", "title": "", "description": "", "phases": [
    { "id": "", "title": "", "tasks": [
      { "id": "", "title": "", "estimated_minutes": 60, "done": false, "completed_at": null, "canvas_position": null, "connections": [], "completion_note": "" }
    ]}
  ]}],
  "routines": [{ "id": "", "title": "", "estimated_minutes": 30 }],
  "days": { "2026-05-07": { "available_hours": 8, "slots": [
    { "task_id": "", "start": "09:00", "end": "09:30", "done": false }
  ], "note": "" }}
}
```

### 关键页面

| 页面 | 功能 |
|------|------|
| 长期计划 | 目标 → 阶段 → 任务树形管理，进度条，日常惯例维护 |
| 今日计划 | 8:00-23:00 时间线，可拖拽任务池，惯例自动生成 |
| 日历 | 月历视图，显示每日完成率 |
| 进度 | 交互式路线图画布（缩放/平移），已完成任务卡片 + 连线 |
| 数据管理 | 数据概览、导入/导出 JSON、清空 |

### 全局状态变量（Stats 路线图）

- `statsCurrentGoalId` — 当前选中的目标 ID
- `statsCanvasScale` — 画布缩放系数（0.3 ~ 3）
- `statsCanvasOffset` — 画布平移偏移量
- `statsIsPanning` / `statsPanStart` — 拖拽平移状态
- `statsConnectingFrom` — 连线起始任务 ID
- `statsDraggedCard` — 当前拖拽的卡片 ID

### 关键交互函数

- `renderStats()` — 渲染整个进度页面（tab + 画布 + 池）
- `renderStatsCanvasCards()` — 渲染画布内卡片（含完成日期、连接柄、拖拽组合）
- `bindCanvasPanEvents()` — 画布平移、drop 定位、坐标转换
- `statsHandleWheel()` / `statsZoomIn/Out/Reset()` — 缩放控制
- `renderStatsConnections()` — SVG 渲染任务连线
- `statsConnectorStart/End()` — 连接柄拖拽创建连线
- `statsOpenModal()` — 打开卡片详情弹窗（编辑完成备注）

## Development

- 无构建工具、无依赖安装、无测试框架
- 直接用浏览器打开 `index.html` 即可运行
- 无需本地服务器（未使用 ES Modules / fetch 等需要服务端的功能）
- 数据存储在浏览器 localStorage 中（key: `planhelper_data`）
- `docs/superpowers/` — 需求文档和计划
