# Changelog

All notable changes to this project will be documented in this file.

The format follows Keep a Changelog.

## [Unreleased]

### Added

- Added GitHub Packages publishing to the release workflow alongside NuGet.org package publishing.

### Changed

- Defaulted permission discovery to `P/permission.json` and automatically included `$(SenlinzPermissionFolder)/**/*.json` for direct package references.
- Added `SenlinzPermissionFolder` so consumers can override the default permission folder without extra `AdditionalFiles` configuration.

## [1.0.0] - 2026-04-27

### Added

- Prepared the first public package set: `Senlinz.Permissions`, `Senlinz.Permissions.Abstractions`, and `Senlinz.Permissions.AspNetCore`.
- Added shorthand `groups` and `permissions` string array support in `permission.json`.
- Added automatic permission group inference from dotted permission codes.
- Added automatic parent group inference for nested groups such as `user` and `user.list`.
- Added bilingual repository readmes plus release documentation assets.
- Added bilingual documentation site entry points for GitHub Pages publishing.

### Changed

- Removed the published JSON schema artifact in favor of structure-first documentation.
- Moved generator build props packaging to `build/` so permission generation stays a direct-reference-only feature.
- Retargeted the generator and runtime support to `.NET 8` build tooling and `netstandard2.0` runtime compatibility.
- Added package metadata, documentation file generation, and symbol package settings for release packaging.
