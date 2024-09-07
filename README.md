# GitHub Action to Extract Release Notes from a Changelog

This action extracts change notes for a single version from a [Keep a
Changelog] file. This can be useful for creating release notes for a GitHub
release. Here's an example from this project's own [release
workflow](.github/workflows/release.yml):

``` yaml
name: 🚀 Release
on:
  push:
    # Release on semantic version tag.
    tags: ['v[0-9]+.[0-9]+.[0-9]+']
permissions:
  contents: write
jobs:
  release:
    name: 🚀 Release on GitHub
    runs-on: ubuntu-latest
    steps:
    - name: Check out the repo
      uses: actions/checkout@v4
    - name: Generate Release Changes
      id: notes
      uses: theory/changelog-version-notes-action@main
    - name: Create GitHub Release
      uses: softprops/action-gh-release@v2
      env: { GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}" }
      with:
        name: Release ${{ github.ref_name }}
        body_path: '${{ steps.notes.outputs.file }}'
```

The resulting file:

``` md
The theme of this release is *Scratching my own itch.*

### ⚡ Improvements

*   First release, based on a script copied from other projects
*   Extracts the log items for a single version from a [Keep a Changelog] file
*   Version header must start with `## [$VERSION]`

### 🏗️ Build Setup

*   Made it a GitHub Action!

### 📚 Documentation

*   Wrote a [README]

  [Keep a Changelog]: https://keepachangelog.com/en/1.1.0/
  [README]: README.md

---

🆚 For more detail compare [changes since cd28465](https://github.com/theory/changelog-version-notes-action/compare/cd28465...v0.1.0).
```

It should work equally well on Linux, macOS, and Windows runners.

## Input Parameters

This action takes the following parameters:

| Key         | Type   | Default            | Description                                         |
| ----------- | ------ | ------------------ |---------------------------------------------------- |
| `version`   | string | ref name (tag)     | Release version to extract from the changelog.      |
| `changelog` | string | `CHANGELOG.md`     | Path to the changelog file.                         |
| `into-file` | string | `release-notes.md` | File to which to write the extracted release notes. |

## Output Parameters

| Key    | Type   | Description                                                    |
| ------ | ------ | -------------------------------------------------------------- |
| `file` | string | File to which to the the extracted release notes were written. |

## How it Works

This action is a [Perl script](./mknotes) that searches the changelog file for
a header matching the release version. It then emits every line until it
reaches the next version header.

The version header must appear at the start of the line and be formatted as a
`##` h2 header with the version in brackets. In other words, for the version
`1.2.0`, it must match:

``` regex
^## \[1\.2\.0]
```

Other text may appear after the bracketed version, such as the date:

``` markdown
## [v1.2.0] — 2024-09-07
```

The script will scan from this line until the next line that starts with
`## `.

### Details

The script has a few additional behavior it may be useful to be aware of:

*   It expects the version to be in brackets, and will ignore its link
    references. For example, if the version is `v1.2.0`, it will ignore lines
    that match `^\s*\[1\.2\.0]:\s+https?:`.
*   Link labels must have their corresponding references appear *before* the
    next version header, or they will not be properly displayed. For example:

    ``` md
    ## [v0.1.1] — 2024-09-17
    *   Used the [example].
    
    [example]: https://example.com

    ## [v0.1.0] — 2024-09-07
    ```

    If that line starting with `[example]:` appeared after the next version
    header, it would not be included in the output.

*   It converts multi-line (wrapped) Markdown list items to single line list
    items. This is to eliminate the unsightly line-wrapping preservation when
    GitHub displays release notes.
*   After the notes have been emitted, it emits a hard break and then an extra
    line linking to changes since the previous release. An example:

    ```md
    ---

    🆚 For more detail compare [changes since cd28465](https://githubcom/foo/bar/compare/v0.1.0...v0.1.1).
    ```

    If there is no previous release, it starts from the first commit in the
    repository.

  [Keep a Changelog]: https://keepachangelog.com/en/1.1.0/
