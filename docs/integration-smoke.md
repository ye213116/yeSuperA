# 联调操作指南：飞书任务与 GitHub 草稿 PR

本文是**操作指南**，只描述联调时应如何按步骤操作，用来说明流程和预期观察点。

> 说明：本文不表示联调已经完成，也不表示相关能力已经实现或验证通过。实际执行前请以代码、
> 配置和真实运行结果为准；任何一步未按预期返回时，应记录结果并按“失败即停止”的原则处理。

## 前置条件

- 飞书侧：自建应用与机器人，配置 App Secret、测试群，以及成员 `open_id` 白名单。
  当前执行器通过群/话题历史轮询获取消息，**不需要**事件订阅或公网回调地址。
- GitHub 侧：限定目标仓库的细粒度用户 PAT，权限为 Contents 读写、Pull requests 读写、
  Issues 读取。本轮不要求宽泛的 classic `repo` scope，也不要求 installation-only 的 App Token。
- 本仓库：完成 `uv sync --extra dev`，并在环境或用户配置中提供可用的模型配置。
- 建议先在测试仓库和非生产飞书群中演练，避免影响真实任务和分支。

## 步骤一：飞书发起任务

1. 在测试群或话题中发送任务消息，**必须真正 @ 机器人**，否则轮询不会把它识别为任务请求。
2. 新 Task 会进入排队状态，需要明确批准 tick 后才会执行；不要期待机器人自动回复受理信息。
3. 记录该 Task ID，后续“原任务续跑”需要对应同一个 Task。

预期观察点：Task 已创建并处于排队状态，批准 tick 后开始执行，后续步骤针对同一 Task ID。

## 步骤二：生成 GitHub 草稿 PR

1. Worker 只修改独立工作区并导出补丁，**不执行** commit、push 或创建 PR。
2. RemoteDelivery 先 `prepare` 一份不可变计划，再核对仓库、专用分支、补丁与 digest。
3. 核对无误后明确批准 `publish`，才会推送专用分支并创建**草稿（draft）**PR。
4. 检查 PR 标题、描述与变更范围是否与任务一致，并确认 PR 仍为 draft。

预期观察点：专用分支与草稿 PR 可见、内容可读，且未经批准不会发生任何远端写入。

## 步骤三：Review 后原任务续跑

1. 在 GitHub 上提交 Review。Review 必须来自允许身份、包含实际修改意见、指向当前完整 head，
   并与当前补丁订阅一致；本账号可提交非空 `COMMENTED` Review。
2. 普通会话评论、空 Review、`APPROVED` 以及指向旧 head 的 Review 都不会自动触发修改。
3. 触发后保持同一 Task，开启新 Run 和新 worktree，更新同一专用分支与同一草稿 PR；
   预算跨 Run 累计。

预期观察点：Task 状态从 `awaiting_review` 回到执行中，Review 意见被处理，PR 持续更新且不丢失
原有变更。

## 步骤四：人工验收

1. 按验收清单逐项检查：功能是否符合需求、改动是否超出范围、测试与检查是否通过。
2. 在本地按项目管理要求运行验证命令，例如：

   ```bash
   uv run --extra dev ruff check .
   uv run --extra dev ruff format --check .
   uv run --extra dev python -m pytest
   ```

3. 注意 `awaiting_review` 不代表完成；只有对当前补丁执行显式 `task accept` 才算验收完成。
   PR 转 ready、合并与 Task accept 是三个不同动作，不能互相替代，且不会自动合并。
4. 不通过则驳回并说明原因，回到步骤三。

预期观察点：验收结论可追溯（谁、何时、依据什么通过或驳回），合并动作始终由人工执行。

## 常见阻塞与处理

- 机器人未响应：确认消息中真正 @ 了机器人，且发送者在 `open_id` 白名单内、群为测试群。
- 无法创建 PR：检查 PAT 的仓库范围与 Contents/Pull requests/Issues 权限，以及分支保护规则。
- Review 后任务未续跑：确认 Review 来自允许身份、非空、指向当前 head，且与补丁订阅一致。
- 验收环境不可用：先修复环境再给出验收结论，不要把环境问题当成通过。
