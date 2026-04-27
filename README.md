# Senlinz.Permissions

**English** | [Chinese](./README.zh-CN.md)

A JSON-driven permission source generator for .NET that generates strongly typed permission constants, catalogs, and optional localization accessors from a single `permission.json` file.

Build-time requirement: .NET 8 SDK or newer (the source generator depends on Roslyn 4.8).

Runtime compatibility: generated runtime support targets `netstandard2.0`, so it can run on .NET Framework 4.6.1+, .NET Core 2.0+, and newer runtimes.

Note: using more traditional C# syntax mainly reduces compiler and tooling requirements; actual runtime compatibility still comes from the `netstandard2.0` target.

- Documentation site: <https://gui-xie.github.io/Senlinz.Permission/>
- Latest package version: `1.0.0`
- Release notes: [RELEASE_NOTES.md](./RELEASE_NOTES.md)
- Changelog: [CHANGELOG.md](./CHANGELOG.md)
- Additional docs: [docs/quick-start.md](./docs/quick-start.md), [docs/permission-file.md](./docs/permission-file.md), [docs/aspnetcore.md](./docs/aspnetcore.md)

## Quick Navigation

- [Features](#features)
- [Package selection](#package-selection)
- [Quick start](#quick-start)
- [Permission file rules](#permission-file-rules)
- [Generated types](#generated-types)
- [ASP.NET Core integration](#aspnet-core-integration)
- [Release and documentation publishing](#release-and-documentation-publishing)

## Features

- Generate nested `Permissions` constants from dotted permission codes.
- Generate `PermissionCatalog` entries for permissions and inferred parent groups.
- Accept shorthand string arrays for `groups` and `permissions` when no extra metadata is needed.
- Infer permission groups automatically from permission code prefixes.
- Generate `PermissionL` accessors when `Senlinz.Localization.Abstractions` is referenced.
- Publish `Senlinz.Permissions`, `Senlinz.Permissions.Abstractions`, and `Senlinz.Permissions.AspNetCore` as separate NuGet packages.

## Package Selection

### `Senlinz.Permissions`

Use this package in consumer projects that need source generation from `permission.json`.

```bash
dotnet add package Senlinz.Permissions
```

Projects that need generated `Permissions.g.cs` should always reference `Senlinz.Permissions` directly. The package ships with `Senlinz.Permissions.Abstractions` runtime support, so a separate abstractions package is only needed when you want the shared contracts without the generator.

### `Senlinz.Permissions.Abstractions`

Use this package only when you need the shared permission contracts without the source generator.

```bash
dotnet add package Senlinz.Permissions.Abstractions
```

### `Senlinz.Permissions.AspNetCore`

Use this package when you want to register generated permissions as ASP.NET Core authorization policies.

```bash
dotnet add package Senlinz.Permissions.AspNetCore
```

## Quick Start

### 1. Reference the packages

```xml
<ItemGroup>
  <PackageReference Include="Senlinz.Permissions" Version="1.0.0" PrivateAssets="all" />
  <PackageReference Include="Senlinz.Permissions.AspNetCore" Version="1.0.0" />
</ItemGroup>
```

If you only need the shared contracts without source generation, reference `Senlinz.Permissions.Abstractions` instead.

### 2. Create `permission.json`

The package automatically adds the configured permission file to `AdditionalFiles` for direct package references. The default file name is `permission.json`.

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

### 3. Use generated members

```csharp
[Authorize(Policy = Permissions.User.Read)]
public IActionResult GetUsers()
{
    return Ok();
}
```

### 4. Register permission policies when needed

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPermissionPolicies(PermissionCatalog.All);
});
```

## Permission File Rules

- `groups` and `permissions` can contain strings when no extra metadata is needed.
- Use object entries only when you need metadata such as `name`, `labelKey`, `requires`, `tags`, or `order`.
- When a permission omits `group`, the generator infers it from the permission code prefix.
- Parent groups are inferred automatically, so `user` becomes the parent group of `user.list`.
- `version` is optional and defaults to `1`.
- Prefer MSBuild properties for namespace and class overrides instead of placing generator-specific metadata in `permission.json`.

## Generated Types

### `Permissions`

- Contains nested constants built from permission code segments.
- `user.list.edit` becomes `Permissions.User.List.Edit`.

### `PermissionCatalog`

- Exposes generated `Groups` and `All` collections.
- `CreateCatalog()` returns a runtime `PermissionCatalog` instance.

### `PermissionL`

- Generated only when localization abstractions are referenced and `SenlinzPermissionGenerateLString` is enabled.
- Uses explicit `labelKey` values when provided and falls back to `permissions.<code>.label`.

## ASP.NET Core Integration

`Senlinz.Permissions.AspNetCore` adds `AuthorizationOptions.AddPermissionPolicies`.

The default policy name is the permission code, and the default claim type is `permission`.

## MSBuild Options

- `SenlinzPermissionFile`: permission JSON path. Default: `permission.json`.
- `SenlinzPermissionNamespace`: generated namespace override.
- `SenlinzPermissionClassName`: constants class name. Default: `Permissions`.
- `SenlinzPermissionCatalogClassName`: catalog class name. Default: `PermissionCatalog`.
- `SenlinzPermissionStrict`: missing file is an error when `true`. Default: `true`.
- `SenlinzPermissionGenerateLString`: generate localization accessors when localization abstractions are referenced. Default: `false`.

## Release And Documentation Publishing

- Keep `README.md`, `README.zh-CN.md`, `docs/README.md`, and `docs/zh-CN/README.md` aligned.
- Update `CHANGELOG.md` and `RELEASE_NOTES.md` before publishing the next package version.
- Validate with `dotnet build Senlinz.Permissions.sln`, `dotnet run --project tests/Senlinz.Permissions.Tests/Senlinz.Permissions.Tests.csproj`, and `dotnet pack -c Release Senlinz.Permissions.sln`.
- Push a `vX.Y.Z` tag to publish `.nupkg` packages to NuGet.org and GitHub Packages automatically.
