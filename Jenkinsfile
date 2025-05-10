pipeline {
    agent { 
        label 'Test-Server' 
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('sngobhe-dockerhub')
    }
    stages {
        stage('Execute Ansible') {
            steps {
                echo 'Executing ansible to install docker on Test Server'
                ansiblePlaybook credentialsId: 'gopi-ansible',
                                 disableHostKeyChecking: true,
                                 installation: 'Ansible',
                                 playbook: 'installdocker.yml'
            }
        }
        stage('Git-Checkout') {
            steps {
                echo 'Checking out the code from GIT Repository'
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ngobinath/projCert-devopsAssignment.git']])
            }
        }
        stage('Docker Build and Push') {
            steps {
                echo 'Logging in to DockerHub'
                // sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                // This step should not normally be used in your script. Consult the inline help for details.
                withDockerRegistry(credentialsId: 'sngobhe-dockerhub') {
                    sh 'docker build -t sngobhe/edurekaassignment1:latest'
                    sh 'docker push'
                }
            }    
        }

    }
    post {
        always {
            sh 'docker logout'
        }
    }
}

