# Java CI/CD Pipeline Using GitHub, Jenkins, Docker, and Kubernetes

A complete, beginner-friendly DevOps project demonstrating end-to-end Continuous Integration and Continuous Deployment (CI/CD) on a local Windows machine without requiring external container registries like Docker Hub.

---

## 🏗️ Architecture Overview

```
 +-------------------------------------------------------+
 |                     GitHub Repo                       |
 |             (Source Code & Jenkinsfile)               |
 +---------------------------+---------------------------+
                             |
                     1. Code Push / Trigger
                             v
 +-------------------------------------------------------+
 |                  Jenkins Pipeline                     |
 |                                                       |
 |  [Stage 1: Checkout SCM]                              |
 |  [Stage 2: Build & Package (Maven -> JAR)]            |
 |  [Stage 3: Docker Build (Local Image Tag: 1.0)]       |
 |  [Stage 4: Deploy to Local Kubernetes (kubectl)]      |
 |  [Stage 5: Verify Deployment (Rollout Status & Pods)] |
 +---------------------------+---------------------------+
                             |
                   Deploy Local Image
                             v
 +-------------------------------------------------------+
 |       Local Kubernetes (Docker Desktop k8s)           |
 |                                                       |
 |   Pod: simple-java-app (imagePullPolicy: IfNotPresent)|
 |   Node: desktop-control-plane                         |
 +-------------------------------------------------------+
```

> **Zero Registry Architecture**: No Docker Hub or third-party container registry is required. The Docker image is built locally into the Docker Desktop engine and directly scheduled onto the local Kubernetes cluster (`desktop-control-plane`) using `imagePullPolicy: IfNotPresent`.

---

## 📁 Project Directory Structure

Project root: `C:\Users\Kaarun T\simple-java-app`

```
C:\Users\Kaarun T\simple-java-app
├── src/
│   └── main/
│       └── java/
│           └── App.java           # Main Java source file
├── App.java                       # Root copy of Java source
├── pom.xml                        # Maven configuration targeting Java 17
├── Dockerfile                     # Container definition using Java 17 JRE
├── deployment.yaml                # Kubernetes Deployment specification
├── Jenkinsfile                    # Declarative Jenkins CI/CD pipeline
├── .gitignore                     # Git ignore rules (targets, IDE files)
└── README.md                      # Complete project documentation
```

---

## ⚙️ Configured Environment

All tools have been configured and verified directly on this machine:

| Component | Version / Path | Status |
| :--- | :--- | :--- |
| **Operating System** | Windows 11 | Verified |
| **Java (Default)** | Temurin OpenJDK 17.0.20.1 (`JAVA_HOME`) | Configured |
| **Java (Secondary)**| Oracle JDK 24 (`C:\Program Files\Java\jdk-24`) | Preserved |
| **Apache Maven** | 3.9.16 (`C:\Users\Kaarun T\Downloads\apache-maven-3.9.16-bin (1)\apache-maven-3.9.16`) | Configured in User `PATH` & `MAVEN_HOME` |
| **Docker Engine** | Docker Desktop 29.9.2 (Server API 1.56) | Running |
| **Kubernetes** | v1.36.1 (Node: `desktop-control-plane`) | Ready |
| **kubectl** | v1.36.1 | Connected & Operational |

---

## 🚀 Manual Verification Commands

You can run any of these commands in a new PowerShell or Command Prompt window:

### 1. Verify Maven & Java Configuration
```powershell
mvn -version
```
Expected output:
```
Apache Maven 3.9.16
Maven home: C:\Users\Kaarun T\Downloads\apache-maven-3.9.16-bin (1)\apache-maven-3.9.16
Java version: 17.0.20.1, vendor: Eclipse Adoptium
```

### 2. Build the Application with Maven
```powershell
cd "C:\Users\Kaarun T\simple-java-app"
mvn clean package
```
This produces `target\simple-java-app-1.0.jar`.

### 3. Test Run the Packaged JAR
```powershell
java -jar target\simple-java-app-1.0.jar
```
Expected output:
```
Java CI/CD Application Started...
Hello from Java CI/CD Pipeline!
```
Press `Ctrl + C` to stop the application.

### 4. Build the Local Docker Image
```powershell
docker build -t simple-java-app:1.0 .
```

### 5. Run & Test the Docker Container
```powershell
docker run -d --name test-simple-app simple-java-app:1.0
docker logs test-simple-app
docker stop test-simple-app
docker rm test-simple-app
```

### 6. Deploy to Local Kubernetes
```powershell
kubectl apply -f deployment.yaml
kubectl rollout status deployment/simple-java-app --timeout=60s
kubectl get pods -l app=simple-java-app
kubectl logs -l app=simple-java-app --tail=20
```

---

## 🐙 Step-by-Step: Push Code to GitHub

1. Open your browser and log into [GitHub](https://github.com).
2. Click **New Repository**:
   - Repository Name: `simple-java-app`
   - Description: `Java CI/CD pipeline with GitHub, Jenkins, Docker, and Kubernetes`
   - Visibility: **Public** or **Private**
   - Do **NOT** initialize with a README, .gitignore, or license (we already created them).
   - Click **Create repository**.
3. In PowerShell, commit the files and push to GitHub:
   ```powershell
   cd "C:\Users\Kaarun T\simple-java-app"
   git add .
   git commit -m "Initial commit: Java CI/CD project with Jenkinsfile, Dockerfile, and k8s deployment"
   git branch -M main
   git remote add origin https://github.com/<YOUR_GITHUB_USERNAME>/simple-java-app.git
   git push -u origin main
   ```

---

## 🛠️ Step-by-Step: Configure Jenkins Manually

### Step 1: Install & Launch Jenkins
If Jenkins is not yet installed:
- Download the generic Windows installer or `jenkins.war` from [jenkins.io](https://www.jenkins.io/download/).
- To run from command line:
  ```powershell
  java -jar jenkins.war --httpPort=8080
  ```
- Open `http://localhost:8080` in your web browser.
- Complete the initial setup wizard and choose **Install suggested plugins** (which includes Git and Pipeline).

### Step 2: Create the CI/CD Pipeline Job
1. On the Jenkins home screen, click **New Item**.
2. Enter the item name: `simple-java-app-pipeline`.
3. Select **Pipeline** as the project type and click **OK**.
4. In the configuration page, scroll down to the **Pipeline** section:
   - **Definition**: Select **Pipeline script from SCM**.
   - **SCM**: Select **Git**.
   - **Repository URL**: `https://github.com/<YOUR_GITHUB_USERNAME>/simple-java-app.git` (or your local repo path `file:///C:/Users/Kaarun T/simple-java-app` for offline testing).
   - **Credentials**: Add your GitHub personal access token or SSH key if the repository is private; leave as `none` if public.
   - **Branch Specifier**: `*/main` (or `*/master`).
   - **Script Path**: `Jenkinsfile`.
5. Click **Save**.

### Step 3: Run the Pipeline
1. Click **Build Now** on the left menu.
2. Watch the stages execute in the **Stage View**:
   - `Checkout`
   - `Build & Test`
   - `Build Docker Image Locally`
   - `Deploy to Local Kubernetes`
   - `Verify Deployment`
3. Click on the build number (e.g. `#1`) > **Console Output** to verify the detailed logs and rollout status.

---

## 💡 Troubleshooting & Notes

- **Docker Hub / Registry Free**: Notice that neither the Dockerfile, Jenkinsfile, nor Kubernetes manifest references Docker Hub. By configuring `imagePullPolicy: IfNotPresent`, Kubernetes immediately uses images built by the local Docker Desktop daemon.
- **Maven SSL Certificates**: On Windows corporate or inspection proxies, Java may require `-Djavax.net.ssl.trustStoreType=WINDOWS-ROOT`. This has been permanently configured in your user environment variable `MAVEN_OPTS`.
