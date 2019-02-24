# GitHub Action for Uploading Release Artifacts

This repository contains a simple GitHub Action implementation, which allows you to upload binaries when a new release is made.

There are two steps:

* Writing a script to generate your binary/binaries.
* Uploading the results.



## Enabling

There are two steps to enable this action:

* Create the file `.github/main.workflow` in your repository.
  * This is where you enable the action, and specify when it will run.
* Create the shell-script `.github/build` in your repository, to run your build step(s)


## Sample Configuration

Here we assume `.github/build` was executed, and generated files such as:

* `puppet-summary-linux-i386`
* `puppet-summary-linux-amd64`
* ..

The result should be that those binaries are uploaded:

```
# Run the magic
action "Upload to releases" {
  uses = "skx/github-action-publish-binaries@master"
  args = "puppet-summary*"
  secrets = ["GITHUB_TOKEN"]
}
```


## Secrets

In your project don't forget to add the secret.
