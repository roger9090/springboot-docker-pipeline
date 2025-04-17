pipeline {
    agent any

   environment {
        DOCKER_IMAGE = 'yourdockerhubusername/springboot-app'
        REMOTE_HOST = "ubuntu@<DOCKER_EC2_PUBLIC_IP>"
        SSH_KEY_ID = "docker-ec2-ssh"
        CONTAINER_NAME = "springboot-app-container"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/roger9090/springboot-docker-pipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh './mvnw test'
            }
        }
      
        stage('Docker Build') {
            steps {
                 script {
            withDockerRegistry(credentialsId: 'dockerhub-credentials') {
                sh '''
                    cd /var/lib/jenkins/workspace/jenkins-project
                    docker build -t jenkins-project:latest -f Dockerfile .
                   '''
                sh "docker tag jenkins-project:latest roger44/jenkins-project:latest"
             }
            }
          }
        }
          stage('Docker Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'dockerhub-credentials') {
                    sh "docker push  roger44/jenkins-project:latest "
                 }
               }
            }
        }
        stage ('Deploying Docker Container') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-credentials') {
                        sh "docker run -d --name jenkins-project -p 8081:8080 roger44/jenkins-project"
                    }
                }
            }
        }
    }
}

