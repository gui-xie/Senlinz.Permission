# Quick Start

1. Add direct package references for generation, runtime abstractions, and optional ASP.NET Core integration.
2. Add `permission.json` to the consuming project root.
3. Start with shorthand strings and switch to object entries only when you need metadata.
4. Use generated constants from the target project's root namespace.
5. Register generated permissions with ASP.NET Core when server authorization policies are needed.

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
    { "code": "user.list.edit", "requires": ["user.read"] }
  ]
}
```

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPermissionPolicies(PermissionCatalog.All);
});
```

`user.read` is assigned to group `user`, and `user.list.edit` is assigned to group `user.list`. Parent groups are inferred automatically.

Permission codes are stable authorization contract values. Rename them only as a breaking application change.
