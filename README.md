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
# Or manually set a repo owner/org:
bin/rename_application your-app-name your-github-username
# Review changes and commit
```

### What's Included

✅ **Rails 8.1** - Latest Rails with modern defaults  
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
The following Kubernetes config was used to deploy into a cluster. The path to this file is the `TARGET_MANIFEST`.
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
      # Utilize these creds if you are using a private repository.
      # This requires a GitHub personal access token (classic) with write:packages permissions.
      # imagePullSecrets:
      #   - name: ghcr-creds
      containers:
      - name: rails-pipeline-example
        image: ghcr.io/davidleechen/rails-pipeline-example:0.0.2 # This image without the tag is the `IMAGE_NAME`.
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

### ArgoCD Integration

The release workflow includes an optional step to automatically update image tags in a separate repository used by ArgoCD for GitOps deployments. When enabled, publishing a release will:

1. Build and push the Docker image to the GitHub Container Registry
2. Automatically update the image tag in your ArgoCD manifest repository
3. Trigger ArgoCD to deploy the new version to your Kubernetes cluster

#### Setup

To enable ArgoCD integration, configure the following in your GitHub repository settings:

**Secrets** (Settings → Secrets and variables → Actions → Secrets):
- `ARGOCD_PAT`: A Personal Access Token with write permissions to your ArgoCD manifest repository. As of this publication that means `Contents: Read/Write`.

**Variables** (Settings → Secrets and variables → Actions → Variables):
- `ARGOCD_USER`: GitHub username for authentication
- `ARGOCD_OWNER`: GitHub owner/organization of the ArgoCD manifest repository
- `ARGOCD_REPO`: Name of the ArgoCD manifest repository
- `IMAGE_NAME`: Full image name (e.g., `ghcr.io/owner/repo`)
- `TARGET_MANIFEST`: Path to the manifest file to update (e.g., `manifests/production.yaml`)

#### How It Works

When a new release is published, the workflow:
1. Clones your ArgoCD manifest repository
2. Updates the image tag in the specified manifest file using `sed`
3. Commits the change with a message like "Update image tag to v1.0.0 via release automation"
4. Pushes the update to the main branch

If `ARGOCD_PAT` is not set, this step is safely skipped, allowing the workflow to function without ArgoCD integration.
