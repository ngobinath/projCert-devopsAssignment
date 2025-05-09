pipeline {
    agent { 
        label 'Test-Server' 
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('sngobhe-dockerhub')
    }
    stages {
        stage('Git-Checkout') {
            steps {
                echo 'Checking out the code from GIT Repository'
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ngobinath/projCert-devopsAssignment.git']])
            }
        }
        stage('Docker Build') {
            steps {
                echo 'Building Docker image..'
                sh 'docker build -t sngobhe/edurekaassignment1:latest .'
            }
        }
        stage('Docker Login') {
            steps {
                echo 'Logging in to DockerHub'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Docker Push') {
            steps {
                echo 'Uploading Image to DockerHub'
                sh 'docker push sngobhe/edurekaassignment1:latest'
            }
        }

        
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
