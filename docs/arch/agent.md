# OpenHands 中的 Agent

Agent 是 OpenHands 框架中的核心执行单元。每个 Agent 都实现了特定的功能，并能够与其他 Agent 协同工作。

## Agent 的基本概念

### Agent 的运行机制
- Agent 在一个循环中运行
- 每次迭代调用 `agent.step()` 方法
- 输入是 [State](../controller/state/state.py) 对象
- 输出是一个 [Action](../events/action) 对象
- 每个 Agent 都有一个 `self.llm` 属性用于与大语言模型交互


### State 对象
State 对象代表了 Agent 在 OpenHands 系统中的运行状态，负责保存和恢复 Agent 会话。它包含以下信息：

1. **多 Agent 状态**：
   - root task（用户与 agent 的对话）
   - subtask（agent 之间或 agent 与用户的对话）
   - 全局和局部迭代计数
   - 多 agent 交互的委托层级
   - 接近卡住的状态

2. **运行状态**：
   - 当前 agent 状态（如 LOADING、RUNNING、PAUSED）
   - 流量控制状态
   - 确认模式
   - 最后遇到的错误

3. **历史记录**：
   - agent 历史中事件的起始和结束 ID
   - 用于检索 agent 的操作和观察结果

4. **指标**：
   - 当前任务的全局指标
   - 当前子任务的局部指标

5. **额外数据**：
   - 特定任务的附加数据

### 可用的 Actions
Agent 可以返回以下类型的 Action（[Action 的定义](../events/action.py)）：

- `CmdRunAction` - 在沙箱终端中运行命令
- `IPythonRunCellAction` - 在 Jupyter notebook 中交互式执行 Python 代码
- `FileReadAction` - 读取文件内容
- `FileWriteAction` - 写入文件内容
- `BrowseURLAction` - 获取 URL 内容
- `AddTaskAction` - 添加子任务
- `ModifyTaskAction` - 修改子任务状态
- `AgentFinishAction` - 停止控制循环
- `AgentRejectAction` - 停止控制循环
- `MessageAction` - 表示来自 agent 或用户的消息

在收到大模型返回的 tool_call 数据之后，由 `openhands/agenthub/codeact_agent/function_calling.py` 转换到对应的 Action 对象。
项目里的 Tool 类只是定义了 Tool 的元数据，Action 类才会定义真正的执行流程，大部分情况是一一对应的，但是也可以实现更复杂的对应关系。

### Observations
Action 执行后会产生相应的 Observation：

- `CmdOutputObservation` - 命令输出
- `BrowserOutputObservation` - 浏览器输出
- `FileReadObservation` - 文件读取结果
- `FileWriteObservation` - 文件写入结果
- `ErrorObservation` - 错误信息
- `SuccessObservation` - 成功信息

1. **工作流程**：
   - Agent 决定使用某个 Tool
   - Agent 创建对应的 Action 实例
   - 系统执行 Action 并调用相应的 Tool
   - Tool 执行操作并产生 Observation
   - Observation 返回给 Agent 进行处理

## Agent 委托机制

OpenHands 是一个多 Agent 系统，Agents 之间可以相互委托任务。例如：
- `CodeActAgent` 可能委托 `BrowsingAgent` 来回答需要浏览网页的问题
- Delegator Agent 可以将任务转发给微型代理，如：
  - `RepoStudyAgent` 用于研究代码库
  - `VerifierAgent` 用于验证任务完成情况


NOTE: 委托机制看起来已经不怎么用了，比如 浏览任务已经从 BrowsingAgent 已经变成了一个普通的 Tool


### 任务和子任务

1. **任务 (Task)**：
   - 是 OpenHands 系统与用户之间的端到端对话
   - 可能包含用户的多次输入
   - 以用户的初始输入开始
   - 以 `AgentFinishAction`、用户停止或错误结束

2. **子任务 (Subtask)**：
   - 是 agent 与用户或其他 agent 之间的端到端对话
   - 如果任务由单个 agent 执行，则任务本身就是一个子任务
   - 一个任务可能包含多个子任务，每个子任务由一个 agent 执行

