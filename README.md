# GitHub Action for Uploading Release Artifacts

This repository contains a simple GitHub Action implementation, which allows you to attach binaries to a new release.

There are two steps to using this action:

* Writing a script to generate your binary/binaries.
  * This must be project specific.  A C-project might just run `make`.
  * A golang-based project might run `go build ..` multiple times for different architectures.
* Create the file `.github/main.workflow` in your repository.
  * This is where you enable the action, and specify the files to add to your release.


## Enabling

There are two steps to enable this action:

* Create the file `.github/main.workflow` in your repository.
  * This is where you enable the action, and specify when it will run.
* Create the shell-script `.github/build` in your repository.
  * This is the script which will be invoked to run your build.
  * It should install any dependencies, and produce the binaries.


## Sample Configuration

This configuration uploads any file in your repository which matches the
shell-pattern `puppet-summary-*`:

```
# pushes
workflow "Handle Release" {
  on = "release"
  resolves = ["Execute"]
}

# Run the magic
action "Execute" {
  uses = "skx/github-action-publish-binaries@master"
  args = "puppet-summary-*"
  secrets = ["GITHUB_TOKEN"]
}
```

We assume that the `.github/build` script generated them.  For example a
go-based project might create files like this using cross-compilation:

* `puppet-summary-linux-i386`
* `puppet-summary-linux-amd64`
* `puppet-summary-darwin-i386`
* `puppet-summary-darwin-amd64`
* ....

The result should be that those binaries are uploaded shortly after you go to
the Github UI and create a new release.
