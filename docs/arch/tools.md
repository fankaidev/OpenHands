# OpenHands 中的 Tools

OpenHands 提供了 Tools 作为执行具体操作的动作接口，是扩展 agent 能力的重要机制之一。

## Tools 的特点
- **目的**：执行具体操作
- **使用方式**：显式函数调用
- **特点**：
  - 具有明确的输入参数和返回值
  - 提供具体的操作能力
  - 需要显式调用
  - 关注"如何做"的任务

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

## 实际 Prompt
* [Langfuse Trace](https://us.cloud.langfuse.com/project/clypdnpot000t99b64wo88muz/traces/openhands-2025-04-16T1151?timestamp=2025-04-16T08%3A10%3A13.070Z&display=details&observation=time-16-12-55-218301_gen-1744791175-rBefDFcHp8bkpwTTLSPa)