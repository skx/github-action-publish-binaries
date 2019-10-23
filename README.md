# GitHub Action for Uploading Release Artifacts

This repository contains a simple GitHub Action implementation which allows you to attach binaries to a new (github) release of your repository.


## Enabling the action

There are two steps required to use this action:

* Enable the action inside your repository.
  * This will mean creating a file `.github/workflows/release.yml` which is where the action is invoked.
  * You'll specify a pattern to describe which binary-artifacts are uploaded.
* Ensure your binary artifacts are generated.
  * Ideally you should do this in your workflow using another action.
  * But if you're in a hurry you can add a project-specific `.github/build` script.


## Sample Configuration: Preferred

The following configuration file uses this action, along with another to build a project.

This is the preferred approach:

```
on: release
name: Handle Release
jobs:
  generate:
    name: Create release-artifacts
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the repository
        uses: actions/checkout@master
      - name: Generate artifacts
        uses: skx/github-action-build@master
      - name: Upload artifacts
        uses: skx/github-action-publish-binaries@master
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          args: 'example-*'
```

This is the preferred approach because it uses a pair of distinct actions, each having one job:

* [skx/github-action-build](https://github.com/skx/github-action-build/)
  * Generates the build artifacts.
  * i.e. Compiles your binaries.
* [skx/github-action-publish-binaries](https://github.com/skx/github-action-publish-binaries)
  * Uploads the previously-generated the build artifacts.

**NOTE**: Please see the note about GITHUB_TOKEN later.



## Sample Configuration: Legacy

In the past this action performed __both__ steps:

* Generated the artifacts
* Uploaded the artifacts

That is still possible, but will be removed when actions come out of beta.

For the moment you can continue to work as you did before, add the script `.github/build` to your repository, and configure this action with a pattern of files to upload.

For example the following usage, defined in `.github/workflows/release.yml`, uploads files matching the pattern `puppet-summary-*`.

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

We assume that the `.github/build` script generated suitable binaries.  For example a go-based project might create files like this using cross-compilation:

* `puppet-summary-linux-i386`
* `puppet-summary-linux-amd64`
* `puppet-summary-darwin-i386`
* `puppet-summary-darwin-amd64`
* ....


**NOTE**: Please see the note about GITHUB_TOKEN later.


## GITHUB_TOKEN

Your workflow configuration file, named `.github/workflows/release.yml`, will contain a reference to `secrets.GITHUB_TOKEN`, however you do __not__ need to generate that, or update your project settings in any way.

You _can_ inject secrets into workflows, defining them in the project settings, and referring to them by name, but the `GITHUB_TOKEN` value is special and it is handled transparently, requiring no manual setup.
