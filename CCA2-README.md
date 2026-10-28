# Continuous Competency Assessment 2 (CCA 2) - CI/CD Pipeline Implementation

## Student Details
- **Student Name**: Keshav Agrawal
- **PRN**: 1262243639
- **Course**: TYCSE - Cloud Computing and DevOps
- **Academic Year**: 2026
- **GitHub Repository**: [keshavagrawal2006/flask-hello-world-devops-project](https://github.com/keshavagrawal2006/flask-hello-world-devops-project)
- **Branch**: `main`

---

## 1. Project Selected
The project selected is **Flask Hello World DevOps Project**, a Python Flask microservice structured with modular testing, containerized packaging via Docker, continuous delivery orchestrated through Jenkins LTS, and automated container deployment onto a Kubernetes (Minikube) cluster.

---

## 2. CI/CD Architecture & Pipeline Stages
The declarative pipeline configured in `Jenkinsfile` consists of 6 automated stages executed upon every Git commit:

1. **Checkout**: Automatically pulls the latest source code revisions and Kubernetes manifests from the GitHub repository (`main` branch).
2. **Build and Test**: Validates Python syntax using `py_compile` and executes unit tests (`test.py`) with `unittest` ensuring endpoint integrity and expected HTTP status codes.
3. **Docker Build**: Builds an optimized production container image based on `python:3.9-slim`, multi-tagging the image with both the unique Jenkins `${BUILD_NUMBER}` and `:latest`.
4. **Push Image**: Authenticates securely against Docker Hub via encrypted Jenkins credentials (`dockerhub-credentials`) and publishes both tagged images (`keshavagrawal2006/flask-devops-app:${BUILD_NUMBER}` and `:latest`).
5. **Minikube Deployment**: Dynamically updates `k8s/deployment.yaml` with the build-specific image tag, applies the deployment and NodePort service manifests (`kubectl apply -f k8s/`), and blocks until zero-downtime rolling update succeeds (`kubectl rollout status`).
6. **Verification**: Inspects active Kubernetes cluster resources (`kubectl get deployments,pods,svc -o wide`) and queries the live application endpoint via NodePort 30008 to confirm system availability.

---

## 3. Infrastructure & Setup
- **Cloud Provider**: Amazon Web Services (AWS EC2)
- **Instance Profile**: `c7i-flex.large` (2 vCPU, 4 GiB RAM), Ubuntu 24.04 LTS, Region: `ap-south-1` (Mumbai)
- **CI Server**: Jenkins LTS running on port 8080
- **Container Runtime**: Docker Engine 26.x
- **Kubernetes Cluster**: Minikube v1.39.0 with Docker driver and automated systemd recovery
- **NodePort Port Forwarding**: socat daemon forwarding external port 30008 to Minikube cluster IP `192.168.49.2:30008`

---

## 4. Kubernetes Manifests
The manifests are located under `k8s/`:
- `k8s/deployment.yaml`: RollingUpdate Deployment managing 2 replicas of `keshavagrawal2006/flask-devops-app`.
- `k8s/service.yaml`: Exposes the pods internally and externally on NodePort `30008`.

---

## 5. Demonstration of Code Change (Version 2)
To demonstrate automated continuous deployment:
1. `app.py` was updated from `"Hello world!!!"` to `"Hello from Keshav Agrawal - Version 2"`.
2. Unit test assertions in `test.py` were adjusted accordingly.
3. The commit was pushed to GitHub `main`.
4. Triggering the pipeline automatically built and deployed the new version, immediately reflected on the live browser URL without manual intervention.
