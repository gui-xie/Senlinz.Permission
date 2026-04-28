# Permission File

`permission.json` only needs two user-facing root properties: `groups` and `permissions`.

## Root structure

- `groups`: optional array of group codes or group objects.
- `permissions`: required array of permission codes or permission objects.
- `version`: optional and defaults to `1`.

## Group entries

A group entry can be a string when no metadata is needed:

```json
{
  "groups": ["user", "user.list"]
}
```

Use an object entry when you need metadata:

```json
{
  "groups": [
    {
      "code": "user.list",
      "name": "User list",
      "labelKey": "permissions.user.list.label",
      "description": "Operations for listing users",
      "order": 10
    }
  ]
}
```

Supported group fields:

- `code`: required for object entries.
- `name`: optional display name.
- `labelKey`: optional localization key.
- `description`: optional description.
- `descriptionKey`: optional description localization key.
- `order`: optional sort order.

## Permission entries

A permission entry can be a string when no metadata is needed:

```json
{
  "permissions": ["user.read", "user.list.edit"]
}
```

Use an object entry when you need metadata:

```json
{
  "permissions": [
    {
      "code": "user.list.edit",
      "group": "user.list",
      "name": "Edit user list",
      "requires": ["user.read"],
      "labelKey": "permissions.user.list.edit.label",
      "tags": ["backend"],
      "order": 20
    }
  ]
}
```

Supported permission fields:

- `code`: required for object entries.
- `name`: optional display name.
- `group`: optional explicit group code.
- `requires`: optional dependency list.
- `description`: optional description.
- `labelKey`: optional localization key.
- `descriptionKey`: optional description localization key.
- `tags`: optional string tags.
- `order`: optional sort order.

## Inference rules

- When `group` is omitted, the generator infers it from the permission code prefix.
- Parent groups are inferred automatically. If `user.list` exists, `user` is added when missing.
- `requires` entries must reference existing permission codes and cannot form cycles.

## Naming rules

Permission codes must match:

```text
^[a-z][a-z0-9]*(\.[a-z][a-z0-9]*)+$
```

Group codes must match:

```text
^[a-z][a-z0-9]*(\.[a-z][a-z0-9]*)*$
```

## MSBuild overrides

Keep generator-specific overrides in MSBuild instead of `permission.json`:

```xml
<PropertyGroup>
  <SenlinzPermissionFolder>P</SenlinzPermissionFolder>
  <SenlinzPermissionFile>permission.json</SenlinzPermissionFile>
  <SenlinzPermissionNamespace>MyApp.Security</SenlinzPermissionNamespace>
  <SenlinzPermissionClassName>Permissions</SenlinzPermissionClassName>
  <SenlinzPermissionCatalogClassName>PermissionCatalog</SenlinzPermissionCatalogClassName>
</PropertyGroup>
```

- `SenlinzPermissionFolder` defaults to `P`.
- `SenlinzPermissionFile` defaults to `permission.json`.
- Direct package references automatically include `$(SenlinzPermissionFolder)/**/*.json` in `AdditionalFiles`.
