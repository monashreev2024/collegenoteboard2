```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'monashree1511/college-notice-board'
        DOCKER_TAG = 'latest'
        DOCKER_CLI = 'C:\\Users\\mona shree\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
    }

    stages {
        stage('Checkout Source') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/monashreev2024/collegenoteboard2.git'
            }
        }

        stage('Validate Files') {
            steps {
                bat '''
                    if not exist index.html exit /b 1
                    if not exist Dockerfile exit /b 1
                    if not exist deployment.yaml exit /b 1
                    if not exist servuce.yaml exit /b 1
                    echo All required files found.
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"%DOCKER_CLI%" build -t %DOCKER_IMAGE%:%DOCKER_TAG% .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER',
                        passwordVariable: 'DH_TOKEN'
                    )
                ]) {
                    bat '''
                        @echo off
                        echo Logging in to Docker Hub...
                        "%DOCKER_CLI%" logout
                        if errorlevel 1 echo Continuing after logout check
                        echo %DH_TOKEN% | "%DOCKER_CLI%" login --username "%DH_USER%" --password-stdin
                        if errorlevel 1 exit /b 1
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat '"%DOCKER_CLI%" push %DOCKER_IMAGE%:%DOCKER_TAG%'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" apply -f deployment.yaml'
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" apply -f servuce.yaml'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get deployments'
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get pods'
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\kubectl.exe" get services'
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: College Notice Board pipeline completed!'
        }
        failure {
            echo 'FAILED: Check the first error in Console Output.'
        }
    }
}
```
