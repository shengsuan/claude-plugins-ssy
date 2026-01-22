[![简体中文](https://img.shields.io/badge/Language-简体中文-red?longCache=true&style=for-the-badge)](./README.md)

# go-zero Plugin for Claude Code

Comprehensive Claude Code plugin for go-zero microservices framework development.

## Overview

This plugin integrates the go-zero AI ecosystem into Claude Code, providing:

- **Knowledge Base**: Comprehensive go-zero patterns, best practices, and troubleshooting
- **MCP Tools**: Direct integration with goctl for code generation and scaffolding
- **Workflows**: Structured guidance for common development tasks

## Features

### Knowledge & Patterns

- REST API development (Handler/Logic/Model architecture)
- gRPC service patterns (service discovery, load balancing)
- Database operations (sqlx, MongoDB, Redis caching)
- Resilience patterns (circuit breaker, rate limiting, load shedding)
- Best practices and troubleshooting guides

### MCP Tools (via mcp-zero)

- `create_api_service`: Generate new REST API services
- `create_rpc_service`: Generate gRPC services from protobuf
- `generate_api_from_spec`: Convert .api files to Go code
- `generate_model`: Create database models from various sources
- `create_api_spec`: Generate API specifications
- `analyze_project`: Analyze existing go-zero projects
- `validate_input`: Validate API specs and configurations
- And more...

## Prerequisites

1. **Go** (1.19 or later)
2. **goctl**: Install with `go install github.com/zeromicro/go-zero/tools/goctl@latest`
3. Ensure `goctl` is in your PATH

## Installation

### For Claude Code

1. Copy this plugin directory to your Claude Code plugins location:
   ```bash
   cp -r go-zero ~/.claude/plugins/
   ```

2. Enable the plugin in Claude Code settings

3. Verify goctl is accessible:
   ```bash
   which goctl
   # Should output path to goctl binary
   ```

### Configuration

If goctl is not in your standard PATH, you can configure the path:

Edit `.mcp.json` and set `GOCTL_PATH`:
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

## Usage

### Getting Started

Once enabled, Claude Code will automatically:
- Recognize go-zero projects and provide contextual assistance
- Offer to generate code using goctl tools
- Apply go-zero best practices and patterns

### Example Workflows

**Create a new API service:**
```
User: Create a user management API service
Claude: I'll create a REST API service for user management...
[Uses create_api_service and generate_api_from_spec MCP tools]
```

**Generate database models:**
```
User: Generate a model for the users table in MySQL
Claude: I'll generate the database model...
[Uses generate_model MCP tool]
```

**Add authentication:**
```
User: Add JWT authentication to my API
Claude: I'll help you add JWT auth following go-zero patterns...
[References skill knowledge and generates code]
```

## Skill Contents

The plugin includes comprehensive knowledge:

- **Getting Started**: Quick start guides for new go-zero developers
- **REST API Patterns**: Handler/Logic/Context architecture, middleware, validation
- **RPC Patterns**: Protocol Buffers, service discovery, error handling
- **Database Patterns**: sqlx operations, caching strategies, connection pooling
- **Resilience Patterns**: Circuit breaker, rate limiting, graceful degradation
- **Best Practices**: Production-ready configuration and deployment
- **Troubleshooting**: Common issues and solutions
- **Examples**: Demo projects and verification scripts

## MCP Tools Reference

### Service Creation

- `create_api_service`: Create new REST API service structure
- `create_rpc_service`: Create new gRPC service structure
- `create_api_spec`: Generate API specification from description

### Code Generation

- `generate_api_from_spec`: Generate Go code from .api file
- `generate_model`: Generate database models (MySQL, PostgreSQL, MongoDB)
- `generate_template`: Generate middleware, error handlers, deployment templates

### Project Management

- `analyze_project`: Analyze existing go-zero project structure
- `validate_input`: Validate .api files and configurations
- `validate_config`: Validate configuration files

### Configuration

- `generate_config_template`: Generate configuration templates

See [mcp-zero documentation](https://github.com/zeromicro/mcp-zero) for detailed tool usage.

## Integration with go-zero Ecosystem

This plugin integrates three key repositories:

1. **[zero-skills](https://github.com/zeromicro/zero-skills)**: Knowledge base and patterns
2. **[ai-context](https://github.com/zeromicro/ai-context)**: Workflows and development guidelines
3. **[mcp-zero](https://github.com/zeromicro/mcp-zero)**: MCP server for goctl integration

## Troubleshooting

### MCP server not starting

1. Verify goctl is installed and in PATH:
   ```bash
   goctl --version
   ```

2. Check MCP server logs in Claude Code debug output

3. Ensure the mcp-zero binary has execute permissions:
   ```bash
   chmod +x ~/.claude/plugins/go-zero/bin/mcp-zero
   ```

### Tools not appearing

1. Restart Claude Code after enabling the plugin
2. Check that .mcp.json is valid JSON
3. Verify GOCTL_PATH environment variable is correct

### Skill not activating

1. Ensure the plugin is enabled in Claude Code settings
2. Verify skills/go-zero-framework/SKILL.md exists
3. Try explicitly asking: "Help me with go-zero development"

## Contributing

This plugin integrates upstream repositories:
- Report issues to respective repositories
- Contribute patterns to [zero-skills](https://github.com/zeromicro/zero-skills)
- Contribute tools to [mcp-zero](https://github.com/zeromicro/mcp-zero)

## License

Apache-2.0 (matching go-zero framework license)

## Resources

- **go-zero Documentation**: https://go-zero.dev
- **go-zero Repository**: https://github.com/zeromicro/go-zero
- **Community**: Join the go-zero developer community

## Version

1.0.0 - Initial release integrating zero-skills, ai-context, and mcp-zero
