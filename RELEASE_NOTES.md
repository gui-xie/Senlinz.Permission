# Release Notes

## 1.0.0 - 2026-04-27

### Highlights

- Initial public release of the Senlinz permissions package set.
- Simplified `permission.json` authoring with string shorthand for `groups` and `permissions`.
- Automatic inference of permission groups and nested parent groups from dotted permission codes.
- Runtime support now targets `netstandard2.0`, and the build line is aligned with the .NET 8 SDK plus Roslyn 4.8.
- Added bilingual documentation site entry points and removed the standalone JSON schema artifact.
- Included runtime abstractions and ASP.NET Core authorization helpers.

### Packages

- `Senlinz.Permissions` `1.0.0`
- `Senlinz.Permissions.Abstractions` `1.0.0`
- `Senlinz.Permissions.AspNetCore` `1.0.0`

### Release Checklist

- Update `CHANGELOG.md`, `RELEASE_NOTES.md`, `README.md`, and `README.zh-CN.md` together.
- Keep `docs/README.md`, `docs/zh-CN/README.md`, and the Docsify sidebars aligned with the repository readmes.
- Run `dotnet build Senlinz.Permissions.sln`.
- Run `dotnet run --project tests/Senlinz.Permissions.Tests/Senlinz.Permissions.Tests.csproj`.
- Run `dotnet pack -c Release Senlinz.Permissions.sln`.
- Create and push the `v1.0.0` tag to publish `.nupkg` packages to NuGet.org and GitHub Packages.
