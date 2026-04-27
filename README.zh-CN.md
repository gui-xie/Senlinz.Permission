# Senlinz.Permissions

[English](./README.md) | **中文**

一个面向 .NET 的权限源生成器。它从单个 `permission.json` 文件生成强类型的 C# 权限常量和权限目录。

构建要求：.NET 10 SDK 或更高版本。

运行时兼容性：

- `Senlinz.Permissions.Abstractions` 目标框架为 `netstandard2.1`。
- `Senlinz.Permissions.AspNetCore` 目标框架为 `net10.0`。

- 当前仓库包版本：`1.0.0`
- 变更日志：[CHANGELOG.md](./CHANGELOG.md)
- 发布说明：[RELEASE_NOTES.md](./RELEASE_NOTES.md)
- 补充文档：[docs/quick-start.md](./docs/quick-start.md)、[docs/json-schema.md](./docs/json-schema.md)、[docs/aspnetcore.md](./docs/aspnetcore.md)

## 包选择

### `Senlinz.Permissions`

需要生成 `Permissions.g.cs` 和 `PermissionCatalog.g.cs` 时，直接在拥有 `permission.json` 的项目中引用这个包。

```bash
dotnet add package Senlinz.Permissions
```

### `Senlinz.Permissions.Abstractions`

只需要 `PermissionDefinition`、`PermissionGroupDefinition`、`PermissionCatalog` 等运行时模型时，引用这个包。

```bash
dotnet add package Senlinz.Permissions.Abstractions
```

### `Senlinz.Permissions.AspNetCore`

需要把生成的权限注册为 ASP.NET Core 授权策略时，引用这个包。

```bash
dotnet add package Senlinz.Permissions.AspNetCore
```

## 快速开始

```xml
<ItemGroup>
  <PackageReference Include="Senlinz.Permissions" Version="1.0.0" PrivateAssets="all" />
  <PackageReference Include="Senlinz.Permissions.Abstractions" Version="1.0.0" />
  <PackageReference Include="Senlinz.Permissions.AspNetCore" Version="1.0.0" />
</ItemGroup>
```

```json
{
  "groups": ["user", "user.list"],
  "permissions": [
    "user.read",
    {
      "code": "user.list.edit",
      "requires": ["user.read"],
      "labelKey": "permissions.user.list.edit.label"
    }
  ]
}
```

规则：

- `groups` 和 `permissions` 在不需要额外元数据时都可以直接写字符串。
- 只有在需要 `name`、`labelKey`、`requires`、`tags`、`order` 等元数据时，才改用对象写法。
- 权限未显式指定 `group` 时，会从权限编码前缀自动推导。
- 父级 group 会自动补全，例如 `user` 会成为 `user.list` 的父 group。
- `version` 不是必填，默认按 `1` 处理。

生成后的权限常量可以直接用于授权特性：

```csharp
[Authorize(Policy = Permissions.User.Read)]
public IActionResult GetUsers()
{
    return Ok();
}
```

ASP.NET Core 中可以这样注册策略：

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPermissionPolicies(PermissionCatalog.All);
});
```

推荐用 MSBuild 属性覆盖命名空间和类名，而不是在 `permission.json` 根节点里放生成器专用配置。