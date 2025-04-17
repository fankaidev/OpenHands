# Memory System

OpenHands 的内存系统由两个主要部分组成：Memory 组件和 Condenser 组件。

## Memory 组件

Memory 组件负责管理代理的记忆和信息检索：

### 核心功能

- 事件流监听和处理
- 工作空间上下文管理
- 知识检索
- 微代理（Microagents）管理

### 主要组件

1. **Memory 类**
   - 处理 RecallAction（回忆动作）
   - 生成 RecallObservation（回忆观察）
   - 管理工作空间和全局微代理
   - 处理工作空间上下文

2. **ConversationMemory 类**
   - 处理事件历史
   - 管理工具调用和消息流
   - 支持视觉功能
   - 提供提示缓存

## Condenser 组件

Condenser 组件负责压缩和管理长期对话历史：

### 核心功能

- 事件历史压缩
- 智能总结生成
- 重要信息保留
- 元数据管理

### 主要实现

1. **基础压缩器**
   - `Condenser`：抽象基类，定义基本接口
   - `RollingCondenser`：处理滚动历史记录

2. **LLM 驱动压缩器**
   - `LLMSummarizingCondenser`：使用 LLM 总结历史
   - `LLMAttentionCondenser`：基于重要性选择事件

3. **专门压缩器**
   - `RecentEventsCondenser`：保留最近事件
   - `ObservationMaskingCondenser`：观察掩码处理
   - `AmortizedForgettingCondenser`：分摊遗忘策略
   - `BrowserOutputCondenser`：浏览器输出处理

### 压缩策略

1. **总结策略**
   - 保留开头重要事件
   - LLM 总结中间事件
   - 保留最近事件
   - 维护压缩比例

2. **注意力策略**
   - 基于重要性选择
     - LLM 对每个事件进行重要性评估
     - 使用结构化提示让 LLM 返回事件 ID 列表
     - 按重要性对事件进行排序
   - 智能排序和过滤
     - 保留前 N 个最重要的事件
     - 自动过滤掉头部已包含的事件 ID
     - 当响应不足时，从尾部补充事件
   - 保持事件连续性
     - 保持固定数量的头部事件（keep_first）
     - 动态调整目标大小（max_size/2）
     - 智能合并头部和重要事件
