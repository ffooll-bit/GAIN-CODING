# CHANGELOG Convention

The `CHANGELOG.md` file follows the Keep a Changelog format. All changes are collected under the `[Unreleased]` category during development; at release time, its content is moved under the new version title.

```
# Changelog

## [Unreleased]

### Added

- _New features, one line each, referencing the issue if applicable._

### Changed

- _Behaviour changes, including any migration notes._

### Fixed

- _Bug fixes, one line each, referencing the issue if applicable._

## [0.1.0] - YYYY-MM-DD

### Added

- Initial release.

[Unreleased]: https://github.com/<owner>/<repo>/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/<owner>/<repo>/releases/tag/v0.1.0
```

## Rules

- Version format follows SemVer: `vMAJOR.MINOR.PATCH`.
- During release, move `[Unreleased]` content under the new version title, create a fresh empty `[Unreleased]` block at the top, and update the comparison links (see Release Process).