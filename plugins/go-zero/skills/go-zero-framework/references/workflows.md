# go-zero Development Workflows

Detailed workflow guides for common go-zero development tasks.

## Workflow 1: New API Service

Complete workflow for creating a new REST API service:

```
1. Ask user for requirements (what entities, what endpoints, what database)
2. Use create_api_spec tool to generate .api specification
3. Show specification to user for approval
4. Use create_api_service tool to create service structure
5. Use generate_api_from_spec tool to generate handlers and logic
6. Implement business logic in Logic layer files
7. Add validation tags to request types
8. Generate comprehensive tests for all handlers
9. Generate README.md including:
   - Service overview and purpose
   - API endpoint documentation with request/response examples
   - Configuration guide with sample YAML
   - Usage examples with curl commands
   - Testing instructions
   - Error codes and handling documentation
10. Generate API.md with detailed endpoint documentation
```

**Key principles:**
- ALWAYS create .api spec before code generation
- Show spec to user for approval before generating
- Generate FULL implementation, not stubs
- Implement complete business logic in Logic layer
- Add validation tags: `validate:"required,email"`
- Generate tests automatically
- ALWAYS generate comprehensive README.md

## Workflow 2: New RPC Service

Complete workflow for creating a new gRPC service:

```
1. Ask user for requirements (what methods, what messages)
2. Use create_rpc_service tool to create service structure
3. Show generated protobuf to user
4. Implement methods in Logic layer
5. Add service discovery configuration if needed
6. Generate comprehensive tests
7. Generate README.md including:
   - Service overview and purpose
   - RPC method documentation
   - Configuration guide
   - Usage examples with grpcurl
   - Testing instructions
8. Generate RPC.md with detailed method documentation
```

## Workflow 3: Add Database Model

Workflow for adding database access to a service:

```
1. Ask user for database type (MySQL, PostgreSQL, MongoDB)
2. Ask for connection details
3. Use generate_model tool with appropriate parameters
4. Add model to ServiceContext struct
5. Inject model in NewServiceContext function
6. Use model in Logic layer with proper error handling
```

**With caching:**
```
1. Follow steps above
2. Use generate_model with cache=true
3. Add cache configuration to config struct
4. Configure Redis connection in etc/config.yaml
```

## Workflow 4: Modify Existing API

Workflow for updating an existing API service:

```
1. Edit the .api file with new endpoints or type changes
2. Use validate_input tool to check syntax
3. Use generate_api_from_spec tool (safe to re-run, won't overwrite logic)
4. Implement new logic in newly generated Logic files
5. Update existing logic if types changed
6. Update tests for new/modified endpoints
7. Update README.md API documentation
```

## Workflow 5: Add JWT Authentication

Workflow for adding authentication to API endpoints:

```
1. Add JWT configuration to config struct:
   type Config struct {
       rest.RestConf
       Auth struct {
           AccessSecret string
           AccessExpire int64
       }
   }

2. Create login endpoint that generates JWT token:
   - Accept username/password
   - Validate credentials
   - Generate token using getJwtToken helper
   - Return token in response

3. Add @server(jwt: Auth) to protected routes in .api file:
   @server(
       jwt: Auth
       group: user
   )
   service user-api {
       @handler getUserProfile
       get /api/users/profile returns (ProfileResponse)
   }

4. Regenerate code with generate_api_from_spec

5. Access userId from context in protected handlers:
   userId := l.ctx.Value("userId").(json.Number).Int64()

6. Update tests for authenticated endpoints

7. Document authentication flow in README.md
```

## Workflow 6: Add Caching

Workflow for adding Redis caching to database models:

```
1. Add cache configuration to config struct:
   type Config struct {
       rest.RestConf
       Cache cache.CacheConf
   }

2. Regenerate model with cache=true:
   Use generate_model tool with cache parameter enabled

3. Update ServiceContext to pass cache config:
   UserModel: model.NewUserModel(conn, c.Cache)

4. Configure cache in etc/config.yaml:
   Cache:
     - Host: localhost:6379

5. Test cache behavior (model automatically uses cache)
```

## Workflow 7: Add Validation

Workflow for adding input validation:

```
1. Add validation tags to request types in .api file:
   type CreateUserRequest {
       Email    string `json:"email" validate:"required,email"`
       Password string `json:"password" validate:"required,min=8"`
       Age      int    `json:"age" validate:"min=18,max=100"`
   }

2. Add Validator to ServiceContext:
   type ServiceContext struct {
       Config    config.Config
       Validator *validator.Validate
   }

3. Initialize Validator in NewServiceContext:
   Validator: validator.New()

4. Call validation in Logic layer:
   if err := l.svcCtx.Validator.StructCtx(l.ctx, req); err != nil {
       return nil, errorx.NewCodeError(400, err.Error())
   }
```

## Decision Matrix

Map user requests to appropriate workflows:

| User Request | Primary Workflow | Additional Workflows |
|--------------|------------------|----------------------|
| Create new API | Workflow 1 | + Workflow 7 (validation) |
| Add database | Workflow 3 | + Workflow 6 (if caching needed) |
| Protect endpoint | Workflow 5 | - |
| Modify existing API | Workflow 4 | - |
| Create RPC service | Workflow 2 | - |
| Add caching | Workflow 6 | - |
| Add validation | Workflow 7 | - |

## Important Rules

### Spec-First Development

- ALWAYS create .api specification before code
- Use create_api_spec tool for new services
- Validate with validate_input before generation
- Show spec to user for approval

### Tool Usage

- Use MCP tools, NOT manual goctl commands
- create_api_service for new API services
- create_rpc_service for new RPC services
- generate_api_from_spec for code from spec
- generate_model for database models

### Implementation Standards

- Generate FULL implementation, not stubs
- Fill Logic layer with complete business code
- Add validation tags to all request types
- Generate comprehensive tests automatically
- Handle all errors with proper status codes

### Documentation Standards

- ALWAYS generate README.md for new services
  - Service overview and purpose
  - Complete API/RPC endpoint documentation
  - Configuration guide with examples
  - Usage examples with curl/grpcurl
  - Testing instructions
  - Error codes and handling
- Generate API.md/RPC.md for detailed endpoint docs
- Include request/response examples
- Document all error codes

### go-zero Conventions

- Context first parameter: `func(ctx context.Context, req *types.Request)`
- Structured errors: `errorx.NewCodeError(code, msg)`
- Configuration defaults: `json:",default=value"`
- Validation tags: `validate:"required,min=3"`
- Three-layer architecture: Handler → Logic → Model

## Common Mistakes to Avoid

### Don't: Empty Stubs

❌ Bad:
```go
func (l *CreateUserLogic) CreateUser(req *types.CreateUserRequest) (*types.CreateUserResponse, error) {
    // TODO: implement logic
    return &types.CreateUserResponse{}, nil
}
```

✅ Good:
```go
func (l *CreateUserLogic) CreateUser(req *types.CreateUserRequest) (*types.CreateUserResponse, error) {
    if err := l.svcCtx.Validator.StructCtx(l.ctx, req); err != nil {
        return nil, errorx.NewCodeError(400, err.Error())
    }

    user := &model.User{
        Email: req.Email,
        Name:  req.Name,
    }

    result, err := l.svcCtx.UserModel.Insert(l.ctx, user)
    if err != nil {
        return nil, errorx.NewCodeError(500, "failed to create user")
    }

    id, _ := result.LastInsertId()
    return &types.CreateUserResponse{
        Id:    id,
        Email: user.Email,
    }, nil
}
```

### Don't: Missing Validation

❌ Bad:
```go
type CreateUserRequest {
    Email string `json:"email"`
}
```

✅ Good:
```go
type CreateUserRequest {
    Email string `json:"email" validate:"required,email"`
}
```

### Don't: Wrong Error Type

❌ Bad:
```go
if err != nil {
    return nil, fmt.Errorf("error: %v", err)
}
```

✅ Good:
```go
if err == model.ErrNotFound {
    return nil, errorx.NewCodeError(404, "user not found")
}
if err != nil {
    return nil, errorx.NewCodeError(500, "internal error")
}
```

### Don't: Manual SQL

❌ Bad:
```go
rows, err := db.Query("SELECT * FROM users WHERE id = ?", id)
```

✅ Good:
```go
// Use generate_model tool to create model, then:
user, err := l.svcCtx.UserModel.FindOne(l.ctx, id)
```

### Don't: Missing Context

❌ Bad:
```go
func CreateUser(req *types.Request) (*types.Response, error) {
    // No context parameter
}
```

✅ Good:
```go
func (l *CreateUserLogic) CreateUser(req *types.Request) (*types.Response, error) {
    // Context available as l.ctx
    user, err := l.svcCtx.UserModel.FindOne(l.ctx, id)
}
```

## Advanced Workflows

### Microservices with RPC Communication

For API service calling RPC service:

```
1. Create RPC service (Workflow 2)
2. Create API service (Workflow 1)
3. Add RPC client configuration to API config
4. Add RPC client to API ServiceContext
5. Call RPC methods from API Logic layer
6. Handle RPC errors appropriately
7. Document service dependencies
```

### Service with Multiple Databases

For service using both MySQL and MongoDB:

```
1. Generate MySQL model with generate_model
2. Generate MongoDB model with generate_model
3. Add both models to ServiceContext
4. Use appropriate model based on data type
5. Handle transactions carefully if needed
```

### Production Deployment

For deploying to production:

```
1. Use generate_template to create Dockerfile
2. Use generate_template to create k8s manifests
3. Configure production settings in etc/ files
4. Set up service discovery (etcd/consul)
5. Configure monitoring and logging
6. Set up circuit breakers and rate limiting
7. Test resilience patterns
```

## Quick Reference

**Common validation tags:**
- `required` - Field must be present
- `email` - Valid email format
- `min=X` - Minimum value/length
- `max=X` - Maximum value/length
- `len=X` - Exact length
- `oneof=a b c` - One of specified values
- `url` - Valid URL format
- `uuid` - Valid UUID format

**Common config patterns:**
- REST: `rest.RestConf`
- MySQL: `sqlx.NewMysql(dataSource)`
- Cache: `cache.CacheConf`
- JWT: `AccessSecret`, `AccessExpire`

**Error status codes:**
- 400: Bad Request (validation failed)
- 401: Unauthorized (auth required)
- 403: Forbidden (insufficient permissions)
- 404: Not Found
- 409: Conflict (duplicate resource)
- 500: Internal Server Error

Follow these workflows for consistent, production-ready go-zero development.
