# Senlinz.Permissions

**English** | [Chinese](./README.zh-CN.md)

A JSON-driven permission source generator for .NET that emits strongly typed C# permission constants and catalogs from a single `permission.json` file.

Build-time requirement: .NET 10 SDK or newer.

Runtime compatibility:

- `Senlinz.Permissions.Abstractions` targets `netstandard2.1`.
- `Senlinz.Permissions.AspNetCore` targets `net10.0`.

- Current repository package version: `1.0.0`
- Changelog: [CHANGELOG.md](./CHANGELOG.md)
- Release notes: [RELEASE_NOTES.md](./RELEASE_NOTES.md)
- Additional docs: [docs/quick-start.md](./docs/quick-start.md), [docs/json-schema.md](./docs/json-schema.md), [docs/aspnetcore.md](./docs/aspnetcore.md)

## Package Selection

### `Senlinz.Permissions`

Use this package in projects that need generated `Permissions.g.cs` and `PermissionCatalog.g.cs`.

```bash
dotnet add package Senlinz.Permissions
```

`Senlinz.Permissions` should be a direct package reference in the project that owns `permission.json`.

### `Senlinz.Permissions.Abstractions`

Use this package when you only need the runtime models such as `PermissionDefinition`, `PermissionGroupDefinition`, and `PermissionCatalog`.

```bash
dotnet add package Senlinz.Permissions.Abstractions
```

### `Senlinz.Permissions.AspNetCore`

Use this package when you want to register generated permissions as ASP.NET Core authorization policies.

```bash
dotnet add package Senlinz.Permissions.AspNetCore
```

## Quick Start

Add direct package references in the consuming project:

```xml
<ItemGroup>
  <PackageReference Include="Senlinz.Permissions" Version="1.0.0" PrivateAssets="all" />
  <PackageReference Include="Senlinz.Permissions.Abstractions" Version="1.0.0" />
  <PackageReference Include="Senlinz.Permissions.AspNetCore" Version="1.0.0" />
</ItemGroup>
```

Create `permission.json` at the project root:

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

Shorthand rules:

- `groups` and `permissions` can contain strings when no extra metadata is needed.
- Use object entries only when you need metadata such as `name`, `labelKey`, `requires`, `tags`, or `order`.
- When a permission omits `group`, the generator infers it from the permission code prefix.
- Parent groups are inferred automatically, so `user` becomes the parent group of `user.list`.
- `version` is optional and defaults to `1`.

Generated constants can be used in authorization attributes:

```csharp
[Authorize(Policy = Permissions.User.Read)]
public IActionResult GetUsers()
{
    return Ok();
}
```

Generated catalogs can be registered with ASP.NET Core:

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPermissionPolicies(PermissionCatalog.All);
});
```

## MSBuild Options

- `SenlinzPermissionFile`: permission JSON path. Default: `permission.json`.
- `SenlinzPermissionNamespace`: generated namespace override.
- `SenlinzPermissionClassName`: constants class name. Default: `Permissions`.
- `SenlinzPermissionCatalogClassName`: catalog class name. Default: `PermissionCatalog`.
- `SenlinzPermissionStrict`: missing file is an error when `true`. Default: `true`.
- `SenlinzPermissionGenerateLString`: generate localization accessors when localization abstractions are referenced. Default: `false`.

Prefer MSBuild properties for namespace and class overrides instead of placing generator-specific metadata in `permission.json`.

## Development

```bash
dotnet build -m:1 Senlinz.Permissions.sln
dotnet run --project tests/Senlinz.Permissions.Tests/Senlinz.Permissions.Tests.csproj
dotnet pack -c Release Senlinz.Permissions.sln
```

The test runner is dependency-free so the repository can validate without downloading external test packages.
