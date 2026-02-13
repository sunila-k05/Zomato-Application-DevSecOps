#  Zomato Application – DevSecOps CI/CD Pipeline
 ![Zomato Clone](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*X_hm5iF0NRjbOZHB6RQIFA.jpeg)  
##  Project Overview

This project demonstrates a complete **Production-Style DevSecOps CI/CD Pipeline** for a React-based Zomato clone application.

The pipeline integrates:

- GitHub (Source Code Management)
- Jenkins (CI/CD Automation)
- OWASP Dependency Check (Dependency Security Scan)
- SonarCloud (Static Code Analysis)
- Docker (Containerization)
- Docker Hub (Image Registry)
- Trivy (Filesystem & Image Vulnerability Scanning)

The goal of this project is to implement a **secure, automated, production-level CI/CD workflow**.

---

#  Architecture Workflow

1. Developer pushes code to GitHub
2. Jenkins triggers pipeline automatically
3. Dependencies installed
4. OWASP Dependency Check scan
5. SonarCloud analysis
6. Docker image build
7. Trivy filesystem scan
8. Trivy image scan
9. Push Docker image to Docker Hub
10. Deploy container

---

#  Tools Used

| Tool | Purpose |
|------|---------|
| Jenkins | CI/CD Automation |
| GitHub | Source Code Management |
| SonarCloud | Code Quality & Static Analysis |
| OWASP Dependency Check | Dependency Vulnerability Scan |
| Docker | Containerization |
| Docker Hub | Image Registry |
| Trivy | File System & Image Security Scan |

---

#  Project Structure

```
.
├── Dockerfile
├── Jenkinsfile
├── package.json
├── package-lock.json
├── src/
├── public/
└── dependency-check-report/
```

---

# Local Setup

## 1️ Clone Repository

```bash
git clone https://github.com/sunila-k05/Zomato-Application-Secure-Deployment-with-Dev-Sec-Ops-CI-CD.git
cd Zomato-Application-Secure-Deployment-with-Dev-Sec-Ops-CI-CD
```

## 2 Install Dependencies

```bash
npm install
```

## 3️ Run Application

```bash
npm start
```

Application runs at:

```
http://localhost:3000
```

---

#  Docker Setup

## Build Docker Image

```bash
docker build -t sunilak05/zomato-app:latest .
```

## Run Docker Container

```bash
docker run -d -p 3001:3000 --name zomato-test sunilak05/zomato-app:latest
```

Access the app at:

```
http://localhost:3001
```

---

#  OWASP Dependency Check

```bash
docker run --rm \
-v $(pwd):/src \
-v dependency-check-data:/usr/share/dependency-check/data \
owasp/dependency-check:latest \
--project Zomato-App \
--scan /src \
--format HTML \
--out /src/dependency-check-report \
--failOnCVSS 11
```

Report Location:

```
dependency-check-report/dependency-check-report.html
```

---

#  Trivy File System Scan

```bash
docker run --rm -v $PWD:/app aquasec/trivy fs /app
```

---

#  Trivy Image Scan

```bash
docker run --rm aquasec/trivy image sunilak05/zomato-app:latest
```

Fail build on HIGH & CRITICAL:

```bash
docker run --rm aquasec/trivy image \
--exit-code 1 \
--severity HIGH,CRITICAL \
sunilak05/zomato-app:latest
```

---

#  SonarCloud Analysis

SonarScanner command used in Jenkins:

```bash
sonar-scanner \
-Dsonar.projectKey=sunila-k05_Zomato-Application-Secure-Deployment-with-Dev-Sec-Ops-CI-CD \
-Dsonar.organization=sunila-k05 \
-Dsonar.host.url=https://sonarcloud.io \
-Dsonar.sources=src \
-Dsonar.token=YOUR_TOKEN
```

SonarCloud Dashboard:
https://sonarcloud.io

---

#  Jenkins Pipeline Stages

1. Checkout SCM
2. Install Dependencies
3. OWASP Dependency Check
4. SonarCloud Analysis
5. Build Docker Image
6. Trivy File Scan
7. Trivy Image Scan
8. Push to Docker Hub
9. Deploy Container

---

#  Issues Faced & Mistakes

##  1. node_modules Committed to GitHub

Mistake:
Committed node_modules directory accidentally.

Fix:
Added `.gitignore`:

```
node_modules/
```

Removed cached files:

```bash
git rm -r --cached node_modules
```

---

##  2. Port 3000 Conflict

Issue:
Local app running on 3000 and Docker container mapped to same port.

Fix:

```bash
docker run -p 3001:3000 ...
```

---

##  3. Docker Container Exiting Immediately

Cause:
Used development server (npm start) instead of production build.

Fix:
Used multi-stage Dockerfile and `serve` for static build.

---

##  4. Node 18 OpenSSL Error

Error:

```
ERR_OSSL_EVP_UNSUPPORTED
```

Cause:
React app incompatible with Node 18 + OpenSSL 3.

Fix:
Changed Docker base image to:

```
node:16-alpine
```

---

##  5. Docker Hub Push Failed

Cause:
Missing credentials in Jenkins.

Fix:
- Added Docker Hub credentials in Jenkins Global Credentials
- Used withCredentials in Jenkinsfile

---

##  6. OWASP Pipeline Failure

Cause:
Used low CVSS threshold.

Fix:
Temporarily changed:

```
--failOnCVSS 11
```

(Production should use 7 or lower)

---

#  Lessons Learned

- Never push node_modules
- Use correct Node version compatibility
- Always tag Docker images with version numbers
- Analyze CI logs carefully
- Enforce security scanning in pipeline
- Avoid using only :latest tag
- Separate dev and production environments

---

#  Future Improvements

- Enforce Sonar Quality Gate
- Fail Trivy on HIGH/CRITICAL vulnerabilities
- Use versioned Docker tags
- Implement staging & production environments
- Deploy to Kubernetes
- Add Monitoring (Prometheus + Grafana)
- Implement Infrastructure as Code (Terraform)

---

#  Final Outcome

✔ Secure DevSecOps CI/CD Pipeline  
✔ Integrated Security Scanning  
✔ Dockerized Application  
✔ Automated Build & Deployment  
✔ Docker Hub Integration  
✔ Production-Level Workflow  

---

##  Author

Sunila K  
DevOps / Cloud / DevSecOps Enthusiast  



---

