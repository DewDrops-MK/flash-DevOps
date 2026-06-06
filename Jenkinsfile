pipeline {
    agent any
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
                dependencyCheck additionalArguments: '--scan ./'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=flask-app \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://192.168.1.110:9000 \
                    -Dsonar.login=sonarqube
                    '''
                }
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }
        stage('Trivy Scan') {
            steps {
                sh 'trivy image $IMAGE_NAME:$BUILD_NUMBER'
            }
        }
        stage('Docker Push') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub']) {
                    sh "docker push $IMAGE_NAME:$BUILD_NUMBER"
                }
            }
        }
    }
}