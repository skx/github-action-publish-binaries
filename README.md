# GitHub Action for Uploading Release Artifacts

This repository contains a simple GitHub Action implementation which allows you to attach binaries to a new (github) release of your repository.

* [GitHub Action for Uploading Release Artifacts](#github-action-for-uploading-release-artifacts)
  * [Enabling the action](#enabling-the-action)
  * [Sample Configuration](#sample-configuration)
  * [Advanced Configuration](#advanced-configuration)
  * [GITHUB_TOKEN](#github_token)


## Enabling the action

There are two steps required to use this action:

* Enable the action inside your repository.
  * This will mean creating a file `.github/workflows/release.yml` which is where the action is invoked.
  * You'll specify a pattern to describe which binary-artifacts are uploaded.
* Ensure your binary artifacts are generated.
  * Ideally you should do this in your workflow using another action.


## Sample Configuration

The following configuration file uses _this_ action, along with the [github-action-build](https://github.com/skx/github-action-build) action to generate the artifacts for a project, then attach them to a release.

```yml
on:
  release:
    types: [created]
name: Handle Release
jobs:
  generate:
    name: Create release-artifacts
    runs-on: ubuntu-latest
    steps:
      - name: Checkout the repository
        uses: actions/checkout@master
      - name: Generate the artifacts
        uses: skx/github-action-build@master
      - name: Upload the artifacts
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


## Advanced Configuration

This action is primarily intended to be invoked upon a release-event, which means that Github itself will create a new release, and the action will upload the specified artifacts to that release.

However it might be that you wish to **create** a new release within an action, then modify it by populating the content and adding artifacts.   This is possible, because we allow you to specify the ID of the release to which your artifacts should be associated.

You'll want to configure it using something like this:

```yml
  upload_artifacts:
    name: Upload Artifacts
    needs: [create_release]
    runs-on: ubuntu-latest
    steps:
      - name: Upload the artifacts
        uses: skx/github-action-publish-binaries@release-1.3
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          releaseId: ${{ needs.create_release.outputs.id }}
          args: '*.bin'
```

Here we're explicitly passing the `releaseId` variable, such that the specified release ID will be used.



## GITHUB_TOKEN

Your workflow configuration file, named `.github/workflows/release.yml`, will contain a reference to `secrets.GITHUB_TOKEN`, however you do __not__ need to generate that, or update your project settings in any way!

You _can_ inject secrets into workflows, defining them in the project settings, and referring to them by name, but the `GITHUB_TOKEN` value is special and it is handled transparently, requiring no manual setup.
