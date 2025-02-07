# Rails Pipeline Example
This repository is a stock rails project created to demonstrate a fully
functional deployment pipeline to Kubernetes.

## Project Creation
The excellent [rails-new](https://github.com/rails/rails-new) project is used
to create a new Rails project without needing local dependencies besides
Docker. That means no messing with Ruby version managers or brew.

```shell
rails-new rails-pipeline-example --ruby-version 3.4.1 --devcontainer
```

## Image
[GitHub Actions](https://github.com/davidleechen/rails-pipeline-example/blob/main/.github/workflows/publish_image_on_release.yml)
are used to build the container image and push it to the GitHub Container Registry.
No special tokens or setup is necessary if kept inside of GitHub because new
GitHub Action workflow runs automatically generate a token that allows pushing to
a repository-scoped container registry.
