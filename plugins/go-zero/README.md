[![English README](https://img.shields.io/badge/Language-EN-blue.svg?longCache=true&style=for-the-badge)](./README.en.md)

# Claude Code 的 go-zero 插件

专为 go-zero 微服务框架开发设计的全面 Claude Code 插件。

## 概览

该插件将 go-zero AI 生态系统集成到 Claude Code 中，提供以下功能：

* **知识库**：全面的 go-zero 模式、最佳实践和故障排除指南。
* **MCP 工具**：通过与 goctl 直接集成，实现代码生成和脚手架功能。
* **工作流**：为常见开发任务提供结构化指导。

## 功能特性

### 知识与模式

* REST API 开发（Handler/Logic/Model 架构）
* gRPC 服务模式（服务发现、负载均衡）
* 数据库操作（sqlx、MongoDB、Redis 缓存）
* 弹性模式（熔断器、限流、负载降级）
* 最佳实践与故障排除指南

### MCP 工具（通过 mcp-zero）

* `create_api_service`：生成新的 REST API 服务
* `create_rpc_service`：从 protobuf 生成 gRPC 服务
* `generate_api_from_spec`：将 .api 文件转换为 Go 代码
* `generate_model`：从各种数据源创建数据库模型
* `create_api_spec`：生成 API 规范
* `analyze_project`：分析现有的 go-zero 项目
* `validate_input`：验证 API 规范和配置
* 以及更多...

## 前提条件

1. **Go** (1.19 或更高版本)
2. **goctl**：通过 `go install github.com/zeromicro/go-zero/tools/goctl@latest` 安装
3. 确保 `goctl` 已添加到系统的 PATH 中

## 安装

### 针对 Claude Code

1. 将此插件目录复制到您的 Claude Code 插件位置：
```bash
cp -r go-zero ~/.claude/plugins/

```


2. 在 Claude Code 设置中启用该插件。
3. 验证 goctl 是否可以访问：
```bash
which goctl
# 应当输出 goctl 二进制文件的路径

```



### 配置

如果 goctl 不在标准 PATH 中，您可以手动配置路径。

编辑 `.mcp.json` 并设置 `GOCTL_PATH`：

```json
{
  "mcpServers": {
    "mcp-zero": {
      "command": "${CLAUDE_PLUGIN_ROOT}/bin/mcp-zero",
      "env": {
        "GOCTL_PATH": "/custom/path/to/goctl"
      }
    }
  }
}

```

## 使用方法

### 快速开始

启用后，Claude Code 将自动：

* 识别 go-zero 项目并提供上下文辅助。
* 提议使用 goctl 工具生成代码。
* 应用 go-zero 最佳实践和模式。

### 示例工作流

**创建一个新的 API 服务：**

```
用户：创建一个用户管理 API 服务
Claude：我将为您创建一个用户管理 REST API 服务...
[使用 create_api_service 和 generate_api_from_spec MCP 工具]

```

**生成数据库模型：**

```
用户：为 MySQL 中的 users 表生成一个模型
Claude：我将生成数据库模型...
[使用 generate_model MCP 工具]

```

**添加身份验证：**

```
用户：给我的 API 添加 JWT 身份验证
Claude：我将按照 go-zero 模式帮您添加 JWT 认证...
[引用技能知识并生成代码]

```

## 技能内容

该插件包含全面的知识库：

* **入门指南**：针对 go-zero 新开发者的快速入门手册。
* **REST API 模式**：Handler/Logic/Context 架构、中间件、校验。
* **RPC 模式**：Protocol Buffers、服务发现、错误处理。
* **数据库模式**：sqlx 操作、缓存策略、连接池。
* **弹性模式**：熔断、限流、优雅降级。
* **最佳实践**：生产级配置与部署。
* **故障排除**：常见问题及解决方案。
* **示例**：演示项目和验证脚本。

## MCP 工具参考

### 服务创建

* `create_api_service`：创建新的 REST API 服务结构。
* `create_rpc_service`：创建新的 gRPC 服务结构。
* `create_api_spec`：根据描述生成 API 规范。

### 代码生成

* `generate_api_from_spec`：从 .api 文件生成 Go 代码。
* `generate_model`：生成数据库模型（MySQL, PostgreSQL, MongoDB）。
* `generate_template`：生成中间件、错误处理器、部署模板。

### 项目管理

* `analyze_project`：分析现有的 go-zero 项目结构。
* `validate_input`：验证 .api 文件和配置。
* `validate_config`：验证配置文件。

### 配置

* `generate_config_template`：生成配置模板。

详细工具用法请参阅 [mcp-zero 文档](https://github.com/zeromicro/mcp-zero)。

## 与 go-zero 生态集成

本插件集成了三个关键仓库：

1. **[zero-skills](https://github.com/zeromicro/zero-skills)**：知识库与模式。
2. **[ai-context](https://github.com/zeromicro/ai-context)**：工作流与开发指南。
3. **[mcp-zero](https://github.com/zeromicro/mcp-zero)**：用于 goctl 集成的 MCP 服务端。

## 故障排除

### MCP 服务未启动

1. 验证 goctl 已安装并处于 PATH 中：`goctl --version`。
2. 检查 Claude Code 调试输出中的 MCP 服务日志。
3. 确保 mcp-zero 二进制文件具有执行权限：
`chmod +x ~/.claude/plugins/go-zero/bin/mcp-zero`

### 工具未显示

1. 启用插件后重启 Claude Code。
2. 检查 `.mcp.json` 是否为有效的 JSON 格式。
3. 验证 `GOCTL_PATH` 环境变量是否正确。

### 技能未激活

1. 确保在 Claude Code 设置中已启用插件。
2. 验证 `skills/go-zero-framework/SKILL.md` 是否存在。
3. 尝试明确提问：“帮我进行 go-zero 开发”。

## 贡献

本插件集成了上游仓库：

* 请向各自的仓库报告问题。
* 向 [zero-skills](https://github.com/zeromicro/zero-skills) 贡献模式。
* 向 [mcp-zero](https://github.com/zeromicro/mcp-zero) 贡献工具。

## 许可证

Apache-2.0（与 go-zero 框架许可证一致）

## 资源

* **go-zero 官方文档**：[https://go-zero.dev](https://go-zero.dev)
* **go-zero 仓库**：[https://github.com/zeromicro/go-zero](https://github.com/zeromicro/go-zero)
* **社区**：加入 go-zero 开发者社区

## 版本

1.0.0 - 初始版本，集成了 zero-skills, ai-context 和 mcp-zero。

