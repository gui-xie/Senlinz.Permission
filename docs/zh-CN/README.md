# Senlinz.Permissions

**中文** | [English](../README.md)

一个面向 .NET 的权限源码生成器。它从单个 `permission.json` 文件生成强类型的权限常量、权限目录，以及可选的本地化访问器。

编译时要求：需要 .NET 8 SDK 或更高版本（源码生成器依赖 Roslyn 4.8）。

运行时兼容性：生成出的运行时支持代码目标框架为 `netstandard2.0`，因此可运行在 .NET Framework 4.6.1+、.NET Core 2.0+ 及更高版本运行时上。

说明：改用更传统的 C# 语法，主要降低的是编译器和工具链门槛；真正决定运行时兼容性的仍然是 `netstandard2.0` 目标框架。

- 文档站点：<https://gui-xie.github.io/Senlinz.Permission/>
- 当前已发布包版本：`1.0.0`

## 快速导航

- [功能特性](#功能特性)
- [包选择](#包选择)
- [快速开始](#快速开始)
- [权限文件规则](#权限文件规则)
- [生成的类型](#生成的类型)
- [ASP.NET Core 集成](#aspnet-core-集成)
- [发布与文档站点](#发布与文档站点)

## 功能特性

- 根据点号分隔的权限编码生成嵌套 `Permissions` 常量。
- 为权限和自动推导出的父级分组生成 `PermissionCatalog` 条目。
- 当不需要额外元数据时，`groups` 和 `permissions` 支持字符串简写数组。
- 权限未显式声明 `group` 时，会按权限编码前缀自动推导。
- 引用 `Senlinz.Localization.Abstractions` 时，可以生成 `PermissionL` 本地化访问器。
- `Senlinz.Permissions`、`Senlinz.Permissions.Abstractions`、`Senlinz.Permissions.AspNetCore` 可以分别作为独立 NuGet 包发布。

## 包选择

### `Senlinz.Permissions`

如果你的项目需要从 `permission.json` 自动生成权限代码，请安装这个包。

```bash
dotnet add package Senlinz.Permissions
```

需要生成 `Permissions.g.cs` 的项目应当直接引用 `Senlinz.Permissions`。这个包会随附 `Senlinz.Permissions.Abstractions` 运行时支持，因此只有在你只想要共享契约而不需要生成器时，才需要单独引用 abstractions 包。

### `Senlinz.Permissions.Abstractions`

如果你只需要共享的权限契约，而不需要源码生成器，请安装这个包。

```bash
dotnet add package Senlinz.Permissions.Abstractions
```

### `Senlinz.Permissions.AspNetCore`

如果你需要把生成的权限注册为 ASP.NET Core 授权策略，请安装这个包。

```bash
dotnet add package Senlinz.Permissions.AspNetCore
```

## 快速开始

### 1. 引用需要的包

```xml
<ItemGroup>
  <PackageReference Include="Senlinz.Permissions" Version="1.0.0" PrivateAssets="all" />
  <PackageReference Include="Senlinz.Permissions.AspNetCore" Version="1.0.0" />
</ItemGroup>
```

### 2. 创建 `P/permission.json`

默认把权限文件放到 `P/` 目录下：

```text
MyProject/
├── P/
│   └── permission.json
└── MyProject.csproj
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

### 3. 使用生成成员

```csharp
[Authorize(Policy = Permissions.User.Read)]
public IActionResult GetUsers()
{
    return Ok();
}
```

### 4. 按需注册权限策略

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPermissionPolicies(PermissionCatalog.All);
});
```

## 权限文件规则

- `groups` 和 `permissions` 在不需要额外元数据时都可以直接写字符串。
- 只有在需要 `name`、`labelKey`、`requires`、`tags`、`order` 等元数据时，才改用对象写法。
- 权限未显式指定 `group` 时，会从权限编码前缀自动推导。
- 父级 group 会自动补全，例如 `user` 会成为 `user.list` 的父 group。
- `version` 不是必填，默认按 `1` 处理。
- 命名空间和类名覆盖更推荐用 MSBuild 属性配置，而不是写在 `permission.json` 根节点里。

## 生成的类型

### `Permissions`

- 包含按照权限编码分段生成的嵌套常量。
- `user.list.edit` 会生成 `Permissions.User.List.Edit`。

### `PermissionCatalog`

- 暴露生成后的 `Groups` 和 `All` 集合。
- `CreateCatalog()` 会返回运行时 `PermissionCatalog` 实例。

### `PermissionL`

- 只有在引用本地化 abstractions 且启用 `SenlinzPermissionGenerateLString` 时才会生成。
- 如果显式提供 `labelKey` 就优先使用，否则回退为 `permissions.<code>.label`。

## ASP.NET Core 集成

`Senlinz.Permissions.AspNetCore` 增加了 `AuthorizationOptions.AddPermissionPolicies` 扩展方法。

默认策略名就是权限编码，默认声明类型是 `permission`。

## MSBuild 选项

- `SenlinzPermissionFolder`：权限文件夹路径。默认值：`P`。
- `SenlinzPermissionFile`：权限 JSON 文件名。默认值：`permission.json`。
- `SenlinzPermissionNamespace`：生成命名空间覆盖值。
- `SenlinzPermissionClassName`：常量类名。默认值：`Permissions`。
- `SenlinzPermissionCatalogClassName`：权限目录类名。默认值：`PermissionCatalog`。
- `SenlinzPermissionStrict`：当为 `true` 时，缺失文件会报错。默认值：`true`。
- `SenlinzPermissionGenerateLString`：当引用本地化 abstractions 时生成本地化访问器。默认值：`false`。

## 发布与文档站点

- 保持 `README.md`、`README.zh-CN.md`、`docs/README.md`、`docs/zh-CN/README.md` 内容同步。
- 发布前同时更新 `CHANGELOG.md` 和 `RELEASE_NOTES.md`。
- 用 `dotnet build Senlinz.Permissions.sln`、`dotnet run --project tests/Senlinz.Permissions.Tests/Senlinz.Permissions.Tests.csproj`、`dotnet pack -c Release Senlinz.Permissions.sln` 做最终验证。
- 推送 `vX.Y.Z` 标签后，会自动把 `.nupkg` 包发布到 NuGet.org 和 GitHub Packages。
