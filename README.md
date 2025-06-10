# app-converter-ops
CI/CD Pipeline Overview
This project uses a Jenkins pipeline to automate build, test, and deployment processes. Below is a summary of the main stages:

Stage	Description
Checkout	Clone the main branch and get the current git commit SHA.
Lint Codebase	Install dependencies and run ESLint to check for code issues.
Build Docker Image	Build a Docker image tagged with the commit SHA version.
Cleanup Existing Container	Stop and remove any running containers with the same name.
Run Docker Container	Start a new container from the built image on port 3000.
Tag and Push Latest	Tag the image as latest for easy reference (pushing is optional).
Notifications
On success, an email is sent to notify that the build and deployment succeeded.
On failure, the container rolls back to the previous latest image and an email is sent with failure details.
