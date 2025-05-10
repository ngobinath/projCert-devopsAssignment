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
                       sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                       echo 'Pushing the Docker Image to Dockerhub'
                       sh 'docker push sngobhe/edurekaassignment1:latest'    
                }
            }    
        }
    }
    post {
        always {
            node ('Test-Server') {
                sh 'docker logout'
            }    
        }
    }
}

