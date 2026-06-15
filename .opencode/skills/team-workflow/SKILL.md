---
name: team-workflow
description: Use when delegating work to subagents or resuming previous subagent sessions. This skill teaches task_id management for cross-call context continuity in long-running multi-agent workflows.
---

# 团队子代理工作流规范

主代理（Build或Plan）调用子代理时必须遵循以下规范，确保跨次调用的上下文连续性。

## 核心原则

使用文件记录子代理的会话 ID，实现跨次调用恢复对话历史。

- 文件位置：`.opencode/.session-<agent-name>`
- 示例：`.opencode/.session-product-manager`

## 每次调用的完整流程

1. **检查**：调用 `task()` 之前，检查 `.opencode/.session-<agent-name>` 是否存在
   - 存在 → 读取内容，作为 `task_id` 参数传入 `task()`
   - 不存在 → 不传 `task_id`，启动全新会话

2. **执行**：`task(subagent_type="<name>", task_id="<id>", prompt="继续之前的工作...")`

3. **记录**：`task()` 返回后，从结果中提取 `task id`，写入 `.opencode/.session-<agent-name>`

## 文件格式

每个文件只包含一行：会话 ID

```
ses_14d01aa55ffeIvTbaq2YNRqYNH
```

## 适用场景

| 角色 | agent 名称 | 工作内容 |
|------|-----------|----------|
| 产品经理 | product-manager | 需求调研、编制《软件需求规格说明书》 |
| 架构师 | architect | 概要设计 |
| 详细设计师 | detail-designer | 详细设计 |
| 开发者 | developer | 编码实现 |
| 测试工程师 | tester | 编写测试用例 |

## 团队共享

本文件在 `.opencode/skills/` 目录下，纳入 git 管理。团队成员克隆仓库后，opencode 自动发现此技能，无需个人配置。
