# Changelog

All notable changes to this project will be documented in this file. It uses the
[Keep a Changelog] format, and this project adheres to [Semantic Versioning].

  [Keep a Changelog]: https://keepachangelog.com/en/1.1.0/
  [Semantic Versioning]: https://semver.org/spec/v2.0.0.html
    "Semantic Versioning 2.0.0"

## [v0.1.2] — 2024-09-09

### 🪲 Bug Fixes

*   Fixed the path so that the action properly finds the script that extracts
    the release notes.

  [v0.1.2]: https://github.com/theory/changelog-version-notes-action/compare/v0.1.1...v0.1.2

## [v0.1.1] — 2024-09-07

### ⚡ Improvements

*   Fixed fetching the earliest commit SHA for the "changes since" line
    appended to the output when working with a shallow clone — as in GitHub's
    action/checkout repositories.

  [v0.1.1]: https://github.com/theory/changelog-version-notes-action/compare/v0.1.0...v0.1.1

## [v0.1.0] — 2024-09-07

The theme of this release is *Scratching my own itch.*

### ⚡ Improvements

*   First release, based on a script copied from other projects
*   Extracts the log items for a single version from a [Keep a Changelog] file
*   Version header must start with `## [$VERSION]`

### 🏗️ Build Setup

*   Made it a GitHub Action!

### 📚 Documentation

*   Wrote a [README]

  [v0.1.0]: https://github.com/theory/changelog-version-notes-action/compare/cd28465...v0.1.0
  [Keep a Changelog]: https://keepachangelog.com/en/1.1.0/
  [README]: README.md
