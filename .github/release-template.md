# {{TAG}} - {{DATE}}

## Highlights

- Update this section with the user-facing changes in {{TAG}}.
- Copy the final summary from RELEASE_NOTES.md when preparing the release branch.

## Packages

- Senlinz.Permissions {{VERSION}}
- Senlinz.Permissions.Abstractions {{VERSION}}
- Senlinz.Permissions.AspNetCore {{VERSION}}

## Validation

- dotnet build Senlinz.Permissions.sln -c Release
- dotnet run --project tests/Senlinz.Permissions.Tests/Senlinz.Permissions.Tests.csproj
- dotnet pack -c Release Senlinz.Permissions.sln

## Notes

- Verify that CHANGELOG.md, RELEASE_NOTES.md, README.md, README.zh-CN.md, docs/README.md, and docs/zh-CN/README.md are aligned.
- Confirm GitHub Pages deployment and NuGet publishing completed successfully.