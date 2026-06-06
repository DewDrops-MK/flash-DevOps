pipeline {
    agent {
        label 'build-agent'
    }
    environment {
        IMAGE_NAME = "dewdropsmk/flask-devops"
    }
    stages {
        stage('Pull Code') {
            steps {
                git 'https://github.com/DewDrops-MK/flash-DevOps.git'
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck odcInstallation: 'OWASP', additionalArguments: '--scan ./ --noupdate'
            }
        }
        stage('SonarQube Analysis') {

    steps {
        withSonarQubeEnv('sonarqube') {

            script {
                def scannerHome = tool 'sonar-scanner'
                sh """
                ${scannerHome}/bin/sonar-scanner \
                -Dsonar.projectKey=flask-devops \
                -Dsonar.projectName=flask-devops \
                -Dsonar.sources=. \
                -Dsonar.sourceEncoding=UTF-8
                """
            }
        }
    }
        }
        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }
        stage('Trivy Scan') {
            steps {
                sh "trivy image ${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }
        stage('Docker Push') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub', url: '']) {
                    sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
    }
}