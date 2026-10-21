# Jenkins CI/CD Pipeline with Docker and AWS ECR

## About the Project

I built this project to get hands-on experience with Jenkins and understand how a CI/CD pipeline works in a real-world scenario.

The idea is simple: keep the application code in GitHub, use Jenkins to build a Docker image, and push that image to Amazon ECR. The image contains a basic HTML webpage served by Nginx.

This project helped me learn how Jenkins, Docker, GitHub, and AWS work together in an automated workflow.

## Tools Used

- **GitHub** – To store and manage the source code
- **Jenkins** – To automate the build and push process
- **Docker** – To package the webpage into a container image
- **Amazon ECR** – To store the Docker image
- **AWS IAM** – To manage access to AWS resources
- **Nginx** – To serve the HTML webpage

## How It Works

1. Jenkins checks out the code from my GitHub repository.
2. It builds a Docker image using the `Dockerfile`.
3. Jenkins authenticates with AWS using credentials stored in Jenkins.
4. The image is tagged with the Amazon ECR repository URI.
5. Jenkins pushes the image to ECR.
6. I can pull the published image and run it to check whether the webpage works.

## Project Structure

```text
ci-cd/
├── Dockerfile
├── Jenkinsfile
├── index.html
└── README.md
```

## AWS Configuration

The project uses the following configuration:

| Setting | Value |
|---|---|
| AWS Region | `ap-south-1` |
| ECR Repository | `task-1` |
| Docker Image | `my-nginx-image` |
| Image Tag | `latest` |

The image is stored in Amazon ECR using this format:

```text
<ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/task-1:latest
```

## Jenkins Credentials

I configured the following credentials in Jenkins:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`

These are stored as **Secret text** credentials and used by the pipeline when connecting to AWS. The keys are not included in the source code.

## Running the Published Image

After the pipeline pushes the image to ECR, I can pull and run it locally.

Log in to ECR:

```bash
aws ecr get-login-password --region ap-south-1 |
docker login --username AWS --password-stdin \
<ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com
```

Pull the image:

```bash
docker pull <ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/task-1:latest
```

Run the container:

```bash
docker run -d --name nginx-web-test -p 8081:80 \
<ACCOUNT_ID>.dkr.ecr.ap-south-1.amazonaws.com/task-1:latest
```

Open `http://localhost:8081` in a browser to view the webpage.

To remove the test container afterwards:

```bash
docker rm -f nginx-web-test
```

## What I Learned

While building this project, I got practical experience with:

- Creating and running a Jenkins pipeline using a `Jenkinsfile`
- Building Docker images from application files
- Configuring Jenkins credentials for AWS access
- Troubleshooting Docker socket permissions
- Authenticating with Amazon ECR and pushing images
- Verifying a published image by running it locally
