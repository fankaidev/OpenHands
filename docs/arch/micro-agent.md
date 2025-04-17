# OpenHands 中的 Micro-agents

Micro-agents 是 OpenHands 提供的一种扩展 agent 能力的机制，主要用作基于关键词触发来为大模型提供补充信息。

## Tools 和 Micro-agents 的区别

### Tools
- **目的**：执行具体操作
- **使用方式**：由大模型指定，然后系统进行调用
- **特点**：
  - 具有明确的输入参数和返回值
  - 提供具体的操作能力
  - 需要显式调用
  - 关注"如何做"的任务

### Micro-agents
- **目的**：提供上下文信息和领域知识
- **使用方式**：当用户输入中包含特定关键词时，系统获取对应的 micro-agent 内容，并添加到对话上下文供大模型使用
- **特点**：
  - 不需要显式函数调用
  - 提供补充信息
  - 通过关键词匹配触发
  - 关注"是什么"和"为什么"的方面

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
3. 这些相关信息会被添加到对话上下文供大模型使用
