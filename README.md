# Rails Pipeline Example

> 🎯 **Template Repository**: Click "Use this template" to create your own Rails application with a complete CI/CD pipeline!

This repository is a stock rails project created to demonstrate a fully
functional deployment pipeline to Kubernetes.

## 🚀 Using as a Template

This repository can be used as a template for new Rails applications. See [TEMPLATE.md](TEMPLATE.md) for detailed instructions on:

- Creating a new application from this template
- Renaming the application using the `bin/rename_application` script
- Configuring deployment settings
- Setting up CI/CD pipelines

**Quick Start:**
```bash
# After creating a repository from this template:
bin/rename_application your-app-name
# Review changes, update GitHub owner (davidleechen → your-username), and commit
```

### What's Included

✅ **Rails 8.0** - Latest Rails with modern defaults  
✅ **Docker** - Production-ready Dockerfile  
✅ **GitHub Actions CI/CD** - Security scanning, linting, testing, and automated releases  
✅ **Kamal Deployment** - Deploy to any server with zero-downtime  
✅ **Dev Container** - Consistent development environment

## Project Creation
The excellent [rails-new](https://github.com/rails/rails-new) project is used
to create a new Rails project without needing local dependencies besides
Docker. That means no messing with Ruby version managers or brew.

```shell
rails-new rails-pipeline-example --ruby-version 3.4.7 --devcontainer
```

## Image
[GitHub Actions](https://github.com/davidleechen/rails-pipeline-example/blob/main/.github/workflows/publish_image_on_release.yml)
are used to build the container image and push it to the GitHub Container Registry.
No special tokens or setup is necessary if kept inside of GitHub because new
GitHub Action workflow runs automatically generate a token that allows pushing to
a repository-scoped container registry.

## Deployment
The following Kubernetes config was used to deploy into a cluster.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rails-pipeline-example
  labels:
    app: rails-pipeline-example
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rails-pipeline-example
  template:
    metadata:
      labels:
        app: rails-pipeline-example
    spec:
      containers:
      - name: rails-pipeline-example
        image: ghcr.io/davidleechen/rails-pipeline-example:0.0.2
        ports:
        - containerPort: 3000
        env:
        - name: RAILS_ENV
          value: "production"
        - name: SECRET_KEY_BASE
          valueFrom:
            secretKeyRef:
              name: project-secrets
              key: rails-pipeline-example-secret-key-base
---
apiVersion: v1
kind: Service
metadata:
  name: rails-pipeline-example-service
spec:
  selector:
    app: rails-pipeline-example
  ports:
    - name: http
      protocol: TCP
      port: 3000
      targetPort: 3000
  type: ClusterIP
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: rails-pipeline-example-ingress
spec:
  rules:
  - host: domain.com
    http:
      paths:
      - backend:
          service:
            name: rails-pipeline-example-service
            port:
              number: 3000
        path: /
        pathType: Prefix
```
