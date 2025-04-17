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

CodeAct 中有两类工具调用方式：

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

#### 示例：浏览器操作

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

浏览器工具提供了一系列 Python 函数来模拟用户操作：
- `goto(url)`: 导航到指定 URL
- `click(bid)`: 点击元素
- `fill(bid, value)`: 填写表单
- `scroll(delta_x, delta_y)`: 滚动页面
等...

这两类调用在流程上其实没有区别，不一样的只是参数的组织方式和复杂程度。