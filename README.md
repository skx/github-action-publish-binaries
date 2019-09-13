# GitHub Action for Uploading Release Artifacts

This repository contains a simple GitHub Action implementation, which allows you to attach binaries to a new release.


## Enabling the action

Enable the action inside your repository by creating a file `.github/workflows/release.yml` which is where the action is invoked, specifying a pattern to describe which binary-artifacts are uploaded.


## Sample Configuration

This configuration uploads any file in your repository which matches the
shell-pattern `puppet-summary-*`, and is defined in the file `.github/workflows/release.yml`:

```
on: release
name: Handle Release
jobs:
  upload:
    name: Upload Artifacts
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - name: Upload
      uses: skx/github-action-publish-binaries@master
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        args: 'puppet-summary-*'
```

We assume that the binaries are already created.  For example a go-based project might create files like this using cross-compilation:

* `puppet-summary-linux-i386`
* `puppet-summary-linux-amd64`
* `puppet-summary-darwin-i386`
* `puppet-summary-darwin-amd64`
* ....

The result should be that those binaries are uploaded shortly after you go to
the Github UI and create a new release.
