pipeline {
    agent none
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerHub')
    }
    stages {
        stage('Execute Ansible') {
            agent { 
                label 'Master' 
            }
            steps {
                echo 'Executing ansible to install docker on Test Server'
                ansiblePlaybook credentialsId: 'gopi-ansible',
                                 disableHostKeyChecking: true,
                                 installation: 'Ansible',
                                 playbook: '/var/tools/playbooks/installdocker.yml'
            }
        }
        stage('Git-Checkout') {
            agent { 
              label 'Test-Server' 
            }
            steps {
                echo 'Checking out the code from GIT Repository'
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ngobinath/projCert-devopsAssignment.git']])
            }
        }
        stage('Docker Build and Push') {
            agent { 
              label 'Test-Server' 
            }
            steps {
                echo 'Logging in to DockerHub'
                // sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                // This step should not normally be used in your script. Consult the inline help for details.
                // This step should not normally be used in your script. Consult the inline help for details.
                withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                        echo 'Building the Docker Image'
                        sh 'docker build -t sngobhe/edurekaassignment1:latest .' 
                        echo 'Pushing the Docker Image to Dockerhub'
                        sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword} docker.io"
                        sh 'docker push sngobhe/edurekaassignment1:latest'    
                }
            }    
        }
        stage('Deploy') {
            agent { 
              label 'Test-Server' 
            }
            steps {
                echo 'Deploying the Image from DockerHub'
                sh 'docker run -d -p 9500:80 sngobhe/edurekaassignment1:latest'
                sh 'curl -v http://18.222.220.172:9500/'
                echo '*************************************************************'
                echo 'The PHP Application Successfully Deployed and tested'
                echo '*************************************************************'
            }
        }
    }
    post {
        failure {
            node ('Test-Server') {
                echo "Failed stage name: ${FAILED_STAGE}"
                sh 'docker rm -v -f $(docker ps -qa)'
                sh 'docker image prune --all --force'
            }    
        }
        always {
            node ('Test-Server') {
                sh 'docker logout'
            }    
        }
    }
    
}

