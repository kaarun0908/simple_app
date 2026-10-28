pipeline {
    agent any

    environment {
        APP_NAME = 'simple-java-app'
        APP_VERSION = '1.0'
        IMAGE_NAME = "${APP_NAME}:${APP_VERSION}"

        // Path configurations for local Windows environment
        JAVA_HOME = 'C:\\Users\\Kaarun T\\AppData\\Local\\Programs\\Eclipse Adoptium\\jdk-17.0.20.101-hotspot'
        MAVEN_HOME = 'C:\\Users\\Kaarun T\\Downloads\\apache-maven-3.9.16-bin (1)\\apache-maven-3.9.16'
        DOCKER_BIN = 'C:\\Users\\Kaarun T\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin'
        MAVEN_OPTS = '-Djavax.net.ssl.trustStoreType=WINDOWS-ROOT'
        KUBECONFIG = 'C:\\Users\\Kaarun T\\.kube\\config'

        // Prepend Java, Maven, Docker, and Kubectl binaries to PATH
        PATH = "${JAVA_HOME}\\bin;${MAVEN_HOME}\\bin;${DOCKER_BIN};${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from Git repository...'
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo 'Compiling Java application and packaging JAR with Maven...'
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image Locally') {
            steps {
                echo "Building Docker image locally (no registry push): ${IMAGE_NAME}..."
                bat "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Deploy to Local Kubernetes') {
            steps {
                echo 'Deploying application to local Docker Desktop Kubernetes cluster...'
                bat 'kubectl apply -f deployment.yaml'
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying rollout status and pod health...'
                bat 'kubectl rollout restart deployment/simple-java-app'
                bat 'kubectl rollout status deployment/simple-java-app --timeout=60s'
                bat 'kubectl get pods -l app=simple-java-app'
            }
        }
    }

    post {
        success {
            echo '========================================================'
            echo 'CI/CD Pipeline Succeeded!'
            echo 'Application built and running on local Kubernetes.'
            echo '========================================================'
        }
        failure {
            echo '========================================================'
            echo 'CI/CD Pipeline Failed! Check the console output above.'
            echo '========================================================'
        }
    }
}
