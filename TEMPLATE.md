# Template Repository Configuration

This repository serves as a template for creating new Rails applications with a complete deployment pipeline to Kubernetes.

## Using This Template

### Step 1: Create Your Repository

1. Click "Use this template" on GitHub to create a new repository
2. Clone your new repository locally

### Step 2: Rename the Application and Set Owner

Run the rename script with your desired application name (in kebab-case):

```bash
# Option 1: Let it auto-detect from your git remote URL
bin/rename_application my-awesome-app

# Option 2: Provide your GitHub username explicitly
bin/rename_application my-awesome-app your-github-username
```

The script will auto-detect your GitHub username from the git remote if not provided.

This will update:
- Ruby module name in `config/application.rb`
- Docker image names in `Dockerfile` and workflows
- Kubernetes configurations in `README.md`
- Kamal deployment configuration in `config/deploy.yml`
- GitHub Actions workflow files
- GitHub owner references throughout the repository

_You could stop here and be on your way to using this repository._

### Step 3: Configure Deployment

Update `config/deploy.yml` with your deployment configuration:

```yaml
# Update these sections:
servers:
  web:
    - YOUR_SERVER_IP  # Replace 192.168.0.1

proxy:
  host: YOUR_DOMAIN.com  # Replace app.example.com

registry:
  username: YOUR_REGISTRY_USERNAME  # For Docker Hub, GitHub Container Registry, etc.
```

### Step 4: Set Up Secrets

Configure the following secrets in your GitHub repository (Settings → Secrets and variables → Actions):

- `KAMAL_REGISTRY_PASSWORD`: Your container registry password/token
- Other secrets as needed for your deployment

### Step 5: Customize

Feel free to modify:
- Add your own models, controllers, and views
- Configure additional services (PostgreSQL, Redis, etc.) in `config/deploy.yml`
- Update Ruby version in `.ruby-version`
- Add more GitHub Actions workflows

## What's Included

This template provides:

- **Rails 8.0**: Modern Rails application setup
- **Docker**: Production-ready Dockerfile
- **GitHub Actions CI/CD**:
  - Security scanning (Brakeman)
  - JavaScript dependency auditing
  - Code linting (RuboCop)
  - Automated testing
  - Docker image building
  - Container registry publishing on release
- **Kamal Deployment**: Configuration for deploying to any server
- **Development Container**: Dev container setup for consistent development environment

## Repository Structure

```
.
├── bin/
│   └── rename_application     # Script to rename the application
├── config/
│   ├── application.rb         # Rails application configuration
│   └── deploy.yml             # Kamal deployment configuration
├── .github/
│   └── workflows/             # CI/CD pipeline definitions
├── Dockerfile                 # Production container image
└── README.md                  # Main documentation
```

## Next Steps After Setup

1. Review and commit your changes:
   ```bash
   git add .
   git commit -m "Configure application from template"
   git push
   ```

2. Create a release to trigger the container image build:
   ```bash
   git tag v0.0.1
   git push origin v0.0.1
   ```
   Or create a release through GitHub's UI

3. Deploy your application:
   ```bash
   bin/kamal setup
   bin/kamal deploy
   ```

## Support

For issues or questions about this template, please refer to the original repository or Rails documentation.
