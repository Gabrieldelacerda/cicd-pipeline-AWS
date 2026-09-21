# CI/CD Pipeline — GitHub Actions, Docker and AWS EC2

A hands-on CI/CD lab built around a simple Flask application.

The application itself is intentionally small. The focus of the project is the delivery pipeline: building and testing a Docker image automatically, publishing versioned images, and deploying them to a remote EC2 instance when deployment is enabled.

## How it works

Every push or pull request to `main` triggers the CI job.

GitHub Actions builds the Docker image, starts a container and sends an HTTP request to the application. The pipeline only continues if the application responds successfully.

For pushes to `main`, the deployment job can also publish the image to Docker Hub and deploy it to AWS EC2. Deployment is controlled through the `ENABLE_EC2_DEPLOY` repository variable, so CI can run independently when no EC2 environment is active.

Images are tagged with both `latest` and the Git commit SHA, making each deployment traceable to a specific commit.

The deployment flow is:

```text
Git push
   ↓
GitHub Actions
   ↓
Build Docker image
   ↓
Run and test container
   ↓
Docker Hub
   ↓
SSH
   ↓
AWS EC2
