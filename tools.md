# OpenHands 中的 Tools 和 Micro-agents

OpenHands 提供了两种主要的机制来扩展 agent 的能力：
1. **Tools**：用于执行具体操作的动作接口
2. **Micro-agents**：基于关键词触发的上下文感知知识提供者

## Tools 和 Micro-agents 的区别

### Tools
- **目的**：执行具体操作
- **使用方式**：显式函数调用
- **特点**：
  - 具有明确的输入参数和返回值
  - 提供具体的操作能力
  - 需要显式调用
  - 关注"如何做"的任务

### Micro-agents
- **目的**：提供上下文信息和领域知识
- **使用方式**：基于关键词的自动触发
- **特点**：
  - 不需要显式函数调用
  - 提供补充信息
  - 通过关键词匹配触发
  - 关注"是什么"和"为什么"的方面


## 为什么需要分开设计 Tools 和 Micro-agents？

1. **不同目的**：
   - Tools：操作能力
   - Micro-agents：上下文知识

2. **不同触发机制**：
   - Tools：显式函数调用
   - Micro-agents：自动关键词匹配

3. **不同集成方法**：
   - Tools：函数调用接口
   - Micro-agents：对话上下文

4. **不同扩展模式**：
   - Tools：需要修改代码
   - Micro-agents：简单内容添加


## 可用的 Tools

### 1. `execute_bash`
- **功能**：执行 Linux bash 命令
- **特性**：
  - 支持后台运行长时间命令
  - 支持交互式进程
  - 管理命令超时
- **使用场景**：
  - 运行系统命令
  - 管理进程
  - 文件系统操作

### 2. `execute_ipython_cell`
- **功能**：在 IPython 环境中运行 Python 代码
- **特性**：
  - 支持魔法命令（如 `%pip`）
  - 维护变量作用域
  - 需要显式导入
- **使用场景**：
  - 数据分析
  - Python 代码执行
  - 交互式开发

### 3. `browser` 和 `web_read`
- **功能**：
  - `web_read`：将网页内容转换为 markdown，用于简单网页抓取
  - `browser`：通过 Python 与网页交互，用于复杂的网页交互任务
- **特性**：
  - 导航
  - 表单填写
  - 文件上传
  - 点击和滚动操作
- **使用场景**：
  - 网页抓取
  - 网页自动化
  - 内容提取

### 4. `str_replace_editor`
- **功能**：通过字符串替换查看和编辑文件
- **特性**：
  - 行号显示
  - 精确字符串匹配
  - 撤销功能
  - 持久化状态
- **使用场景**：
  - 文件编辑
  - 代码修改
  - 配置更改

### 5. `edit_file` (基于 LLM)
- **功能**：使用 LLM 内容生成编辑文件
- **特性**：
  - 部分文件编辑
  - 行范围指定
  - 追加模式
- **使用场景**：
  - 复杂代码修改
  - 大文件编辑
  - 内容生成

## 可用的 Micro-agents

系统包含以下专门的 micro-agents：

1. **npm**：处理 npm 包安装，提供非交互式 shell 的解决方案和自动确认操作
2. **github**：管理 GitHub 操作，支持 API 认证、PR 创建和分支管理
3. **docker**：提供 Docker 容器管理、镜像构建和 Compose 使用指导
4. **kubernetes**：处理 Kubernetes 集群管理、部署配置和资源管理
5. **swift-linux**：在 Linux 环境下处理 Swift 开发、编译和依赖管理
6. **pdflatex**：处理 LaTeX 文档编译、PDF 生成和错误处理
7. **security**：提供安全最佳实践、敏感信息处理和权限管理指导
8. **add_agent**：指导如何创建和集成新的 micro-agent
9. **flarglebargle**：处理特殊请求的彩蛋响应处理器



## Micro-agent 触发场景示例

### npm Micro-agent 触发示例

**用户输入**：
```
我需要安装一个 npm 包，但是遇到了权限问题
```

**触发过程**：
1. 系统检测到关键词 "npm"
2. npm Micro-agent 自动触发，提供相关信息：
   ```
   <EXTRA_INFO>
   The following information has been included based on a keyword match for "npm".
   It may or may not be relevant to the user's request.

   When using npm to install packages, you will not be able to use an interactive shell, and it may be hard to confirm your actions.
   As an alternative, you can pipe in the output of the unix "yes" command to confirm your actions.
   </EXTRA_INFO>
   ```

**结果**：
- 用户获得关于 npm 非交互式安装的解决方案
- 系统建议使用 `yes` 命令来自动确认操作
- 不需要显式调用任何工具


## Micro-agent 内容集成机制
Micro-agent 的内容会被集成到对话历史中，具体机制如下：

1. **内容集成**：
   - 通过 `ConversationMemory` 实例管理消息历史
   - 当检测到关键词匹配时，micro-agent 的内容会被格式化为 `<EXTRA_INFO>` 标签包裹的文本
   - 内容会被添加到对话上下文中，影响后续对话

2. **触发机制**：
   - 在用户消息中检测关键词
   - 匹配到特定关键词时触发相应的 micro-agent
   - 触发的内容会被添加到对话历史中

3. **内容格式**：
   ```
   <EXTRA_INFO>
   The following information has been included based on a keyword match for "关键词".
   It may or may not be relevant to the user's request.

   具体内容...
   </EXTRA_INFO>
   ```

4. **持久化与影响**：
   - 内容会被持久化到对话历史中
   - 作为上下文信息提供给大模型
   - 影响后续对话的准确性和连贯性