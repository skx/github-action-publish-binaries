# GitHub Action for Uploading Release Artifacts

This repository contains a simple GitHub Action implementation, which allows you to attach binaries to a new release.

There are two steps to using this action:

## Enabling the action

There are two steps required to use this action:

* Enable the action inside your repository.
  * This will mean creating a file `.github/workflows/release.yml`.
* Add your project-specific `.github/build` script.
  * This is the script which will generate the binaries this action will upload.
    * A C-project might just run `make`.
    * A golang-based project might run `go build .` multiple times for different architectures.


## Sample Configuration

This configuration uploads any file in your repository which matches the
shell-pattern `puppet-summary-*`, and is defined in the file `.github/workflows/release.yml`:

```
on: release
name: Handle Release
jobs:
  upload:
    name: Upload
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

We assume that the `.github/build` script generated suitable binaries.  For example a go-based project might create files like this using cross-compilation:

* `puppet-summary-linux-i386`
* `puppet-summary-linux-amd64`
* `puppet-summary-darwin-i386`
* `puppet-summary-darwin-amd64`
* ....

The result should be that those binaries are uploaded shortly after you go to
the Github UI and create a new release.
