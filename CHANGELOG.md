# Changelog

All notable changes to this project will be documented in this file.

The format follows Keep a Changelog.

## [Unreleased]

## [1.0.0] - 2026-04-27

### Added

- Prepared the first public package set: `Senlinz.Permissions`, `Senlinz.Permissions.Abstractions`, and `Senlinz.Permissions.AspNetCore`.
- Added shorthand `groups` and `permissions` string array support in `permission.json`.
- Added automatic permission group inference from dotted permission codes.
- Added automatic parent group inference for nested groups such as `user` and `user.list`.
- Added bilingual repository readmes plus release documentation assets.

### Changed

- Simplified the published permission schema reference so it focuses on `groups` and `permissions`.
- Moved generator build props packaging to `build/` so permission generation stays a direct-reference-only feature.
- Added package metadata, documentation file generation, and symbol package settings for release packaging.