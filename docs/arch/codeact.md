# CodeAct: 统一代码行为空间的智能体框架

CodeAct 是一个基于 [论文](https://arxiv.org/abs/2402.01030) 实现的智能体框架，其核心思想是将 LLM (大语言模型) 智能体的所有行为（actions）统一到一个基于代码的行为空间中。

## 基本概念

CodeAct Agent 在每个回合可以执行两种主要行为：

1. **Converse（对话）**：
   - 用自然语言与人类进行交流
   - 用于询问澄清、确认等交互目的

2. **CodeAct（代码行为）**：
   - 通过执行代码来完成任务
   - 支持多种类型的代码执行

## 工具系统

CodeAct Agent 通过一系列预定义的工具来执行任务：

### 基础工具（默认启用）
- `execute_bash`：执行 Linux bash 命令
- `think`：思考和规划
- `finish`：结束任务

### 可选工具（通过配置启用）

1. **浏览器相关**（`codeact_enable_browsing=True`）：
   - `web_read`：读取和转换网页内容为 markdown
   - `browser`：通过 Python 代码与网页交互

2. **Python 执行**（`codeact_enable_jupyter=True`）：
   - `execute_ipython_cell`：在 IPython 环境中执行 Python 代码

3. **文件编辑**（`codeact_enable_llm_editor=True`）：
   - `edit_file`：基于 LLM 的文件编辑
   - `str_replace_editor`：基于字符串替换的文件编辑

## 代码方式 vs 工具调用

CodeAct 中有两种不同的工具实现方式：

### 1. 普通工具调用

特点：
- 每次调用执行一个独立的操作
- 参数简单，通常是基本类型（字符串、数字等）
- 工具之间相互独立，没有状态共享
- 每个工具都有特定的功能边界

示例：
```python
# WebReadTool 的调用
{
    'name': 'web_read',
    'arguments': {
        'url': 'https://example.com'
    }
}

# 执行 bash 命令的工具调用
{
    'name': 'execute_bash',
    'arguments': {
        'command': 'ls -l'
    }
}
```

### 2. 代码方式

特点：
- 可以在一次调用中执行多个连续的操作
- 使用编程语言语法，支持更复杂的操作序列
- 操作之间可以共享状态
- 提供完整的 API 集合，可以组合使用

#### 示例 1：浏览器操作

```python
# BrowserTool 的调用
{
    'name': 'browser',
    'arguments': {
        'code': '''
        goto("https://example.com/login")
        fill("username-field", "myusername")
        fill("password-field", "mypassword")
        click("login-button")
        noop(1000)
        '''
    }
}
```

浏览器工具提供了 15 个 Python 函数来模拟用户操作：
- `goto(url)`: 导航到指定 URL
- `click(bid)`: 点击元素
- `fill(bid, value)`: 填写表单
- `scroll(delta_x, delta_y)`: 滚动页面
等...

#### 示例 2：IPython 执行

```python
# 数据处理示例
{
    'name': 'execute_ipython_cell',
    'arguments': {
        'code': '''
        import pandas as pd
        import matplotlib.pyplot as plt

        # 创建数据
        df = pd.DataFrame({'x': [1, 2, 3], 'y': [4, 5, 6]})

        # 数据处理
        df['z'] = df['x'] * df['y']

        # 绘图
        plt.plot(df['x'], df['y'])
        plt.savefig('plot.png')
        '''
    }
}
```

## 代码方式的优势

1. **复杂度层次**：
   - 提供了迷你编程环境，可以组合多个操作
   - 支持更复杂的控制流程

2. **状态管理**：
   - 维护会话状态
   - 操作之间可以共享数据

3. **表达能力**：
   - 可以通过编程实现复杂逻辑
   - 支持条件判断和循环

4. **灵活性**：
   - 可以灵活组合各种操作
   - 支持自定义操作序列

5. **错误处理**：
   - 可以加入更复杂的错误处理逻辑
   - 支持异常处理和重试机制

## 设计优点

1. **简单性**：通过预定义的工具集简化了复杂任务
2. **可靠性**：工具都经过了充分测试和封装
3. **安全性**：代码执行在受控环境中
4. **灵活性**：可以根据需要启用或禁用不同的工具

## 结论

CodeAct 的设计通过将复杂的任务分解成代码执行的形式，使得智能体的行为更加可预测和可靠。虽然 agent 确实会编写一些代码，但大部分情况下是通过调用这些预定义的工具来完成任务，而不是完全依赖于自己编写新代码。这种设计既保证了执行的可靠性，又保持了足够的灵活性。