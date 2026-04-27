# JSON File Reference

`docs/schemas/permissions-v1.schema.json` intentionally documents only the two user-facing root properties: `groups` and `permissions`.

Required root properties:

- `permissions`: array of permission codes or permission objects.

Optional root properties:

- `groups`: array of group codes or group objects.

Shorthand rules:

- A group can be a string like `"user.list"` or an object when you need metadata such as `name`, `labelKey`, `description`, or `order`.
- A permission can be a string like `"user.list.edit"` or an object when you need metadata such as `requires`, `tags`, `labelKey`, or a custom `group`.
- When `group` is omitted, the generator infers it from the permission code prefix.
- Parent groups are added automatically, so `user` becomes the parent group of `user.list`.
- `version` is optional and defaults to `1` when omitted.

Permission codes must match:

```text
^[a-z][a-z0-9]*(\.[a-z][a-z0-9]*)+$
```

Group codes must match:

```text
^[a-z][a-z0-9]*(\.[a-z][a-z0-9]*)*$
```

`requires` entries must reference existing permission codes and cannot form a cycle.

Generated namespace and class overrides should be configured through MSBuild properties instead of the JSON file.
