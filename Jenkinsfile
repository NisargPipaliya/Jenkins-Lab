pipeline {
    agent any
    stages {
        stage("checkout") {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/NisargPipaliya/Jenkins-Lab']])
            }
        }
        stage("Install Docker") {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                        if ! [ -x "$(command -v docker)" ]; then
                            echo "Docker is not installed. Installing Docker..."
                            sudo apt-get update
                            sudo apt-get install -y docker.io
                            sudo systemctl start docker
                            sudo systemctl enable docker
                        else
                            echo "Docker is already installed."
                        fi
                        '''
                    } else {
                        bat '''
                        docker --version || (
                            echo "Docker is not installed. Installing Docker..."
                            powershell "Install-Package -Name docker -ProviderName DockerMsftProvider -Force"
                        )
                        '''
                    }
                }
            }
        }
        stage("Build Image") {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker build -t nisargpipaliya/dockerpipeline .'
                    } else {
                        bat 'docker build -t nisargpipaliya/dockerpipeline .'
                    }
                }
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: '24', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    script {
                        if (isUnix()) {
                            sh 'docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                            sh 'docker push nisargpipaliya/dockerpipeline'
                            sh 'docker logout'
                        } else {
                            bat 'docker login -u %DOCKERHUB_USERNAME% -p %DOCKERHUB_PASSWORD%'
                            bat 'docker push nisargpipaliya/dockerpipeline'
                            bat 'docker logout'
                        }
                    }
                }
            }
        }
    }
}
