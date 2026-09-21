CI/CD Pipeline — GitHub Actions, Docker and AWS EC2

This project is a small CI/CD lab built around a simple Flask application. The application itself is intentionally basic because the main focus is the delivery process around it.

A push or pull request to the main branch starts the GitHub Actions workflow. The first job builds the Docker image, starts a temporary container and checks whether the application actually responds before the pipeline is allowed to continue.

If deployment is enabled, the second job logs in to Docker Hub, builds and publishes the image, connects to an AWS EC2 instance over SSH and replaces the running container with the new version. Images are tagged with both latest and the Git commit SHA, so a deployment can be tied back to the exact commit that produced it.

The deployment step can be turned on or off with the ENABLE_EC2_DEPLOY repository variable. This lets the CI part keep running even when there is no EC2 instance active.

The main stack used here is GitHub Actions, Docker, Docker Hub, AWS EC2, SSH, Python and Flask.

To run the application locally:

docker build -t cicd-pipeline-aws .

docker run --rm -p 5000:5000 cicd-pipeline-aws

Then test it with:

curl http://localhost:5000

Expected response:

{"message":"CI/CD pipeline working!","status":"ok"}

The workflow itself is stored at:

.github/workflows/deploy.yml

The project was revalidated end to end using a temporary AWS EC2 instance. During that validation, the pipeline successfully built and tested the application, published the Docker image, connected to EC2 over SSH, deployed the container and returned a successful HTTP response from the running application.

The temporary AWS infrastructure used for the validation was removed after testing.

Evidence from the validation is available in the screenshots folder:

screenshots/pipeline-success.png
screenshots/deployment-response.png
