# Flask DevOps CI/CD Project

This project is a simple Flask application used to demonstrate a complete DevOps workflow with Docker, Jenkins, security scanning, image publishing, and Kubernetes deployment.

The application listens on port `5000` and returns a success message with the hostname of the pod or container that served the request.

## Project Structure

```text
.
├── app.py
├── Dockerfile
├── Jenkinsfile
├── requirements.txt
├── README.md
└── k8s
    ├── deployment.yaml
    └── service.yaml
```

## Application

`app.py` defines a small Flask web server:

- Route: `/`
- Host: `0.0.0.0`
- Port: `5000`
- Response includes the container or pod hostname

## Prerequisites

- Python 3.8+
- Docker
- Jenkins with a build agent labeled `build-agent`
- Kubernetes cluster
- `kubectl` configured for the target cluster
- Docker Hub credentials configured in Jenkins as `dockerhub`
- SonarQube configured in Jenkins as `sonarqube`
- Sonar scanner tool configured as `sonar-scanner`
- OWASP Dependency Check configured as `OWASP`
- Trivy installed on the Jenkins build agent

## Run Locally

Install dependencies:

```bash
pip install -r requirements.txt
```

Start the Flask app:

```bash
python app.py
```

Open:

```text
http://localhost:5000
```

## Docker

Build the image:

```bash
docker build -t dewdropsmk/flask-devops:local .
```

Run the container:

```bash
docker run -p 5000:5000 dewdropsmk/flask-devops:local
```

Open:

```text
http://localhost:5000
```

## Jenkins CI/CD Pipeline

The `Jenkinsfile` defines the following stages:

1. Pull code from GitHub
2. Run OWASP Dependency Check
3. Run SonarQube analysis
4. Build Docker image
5. Scan Docker image with Trivy
6. Push Docker image to Docker Hub

The Docker image name is:

```text
dewdropsmk/flask-devops
```

The Jenkins build number is used as the image tag:

```text
dewdropsmk/flask-devops:${BUILD_NUMBER}
```

## Kubernetes Deployment

The Kubernetes manifests are stored in the `k8s/` directory.

Current deployment settings:

- Namespace: `flask-app`
- Deployment name: `flask-app`
- Replicas: `3`
- Container port: `5000`
- Service type: `NodePort`
- NodePort: `30007`

Create the namespace if it does not already exist:

```bash
kubectl create namespace flask-app
```

Apply the deployment:

```bash
kubectl apply -f k8s/deployment.yaml
```

Apply the service:

```bash
kubectl apply -f k8s/service.yaml
```

Check the resources:

```bash
kubectl get all -n flask-app
```

Access the application:

```text
http://<node-ip>:30007
```

For Minikube, you can also use:

```bash
minikube service flask-app -n flask-app
```

## Useful Commands

View pods:

```bash
kubectl get pods -n flask-app
```

View service:

```bash
kubectl get svc -n flask-app
```

View logs:

```bash
kubectl logs -l app=flask-app -n flask-app
```

Delete the application resources:

```bash
kubectl delete -f k8s/service.yaml
kubectl delete -f k8s/deployment.yaml
```
