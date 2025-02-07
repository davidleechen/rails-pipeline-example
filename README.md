# Rails Pipeline Example
This repository is a stock rails project created to demonstrate a fully
functional deployment pipeline to Kubernetes.

## Creation
The excellent [rails-new](https://github.com/rails/rails-new) project is used
to create the a new Rails project without needing local dependencies besides
Docker. That means no messing with Ruby version managers or brew.

```shell
rails-new rails-pipeline-example --ruby-version 3.4.1 --devcontainer
```
