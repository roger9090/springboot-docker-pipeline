pipeline {
    agent any

   environment {
        DOCKER_IMAGE = 'roger44/springboot-app'
        REMOTE_HOST = "ubuntu@54.241.221.109"
        SSH_KEY_ID = "ec2-ssh-key"
        CONTAINER_NAME = "springboot-app"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/roger9090/springboot-docker-pipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }
        stage('Test') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw test'
           }
        }
      
        stage('Docker Build') {
            steps {
                 script {
            withDockerRegistry(credentialsId: 'dockerhub-credentials') {
                sh '''
                    cd /home/ubuntu/springboot-docker-pipeline
                    docker build -t springboot-app:latest -f Dockerfile .
                   '''
                sh "docker tag springboot-app:latest roger44/springboot-app:latest"
             }
            }
          }
        }
          stage('Docker Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'dockerhub-credentials') {
                    sh "docker push  roger44/springboot-app:latest "
                 }
               }
            }
        }
        stage ('Deploying Docker Container') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-credentials') {
                        sh "docker run -d --name springboot-app -p 8081:8080 roger44/springboot-app"
                    }
                }
            }
        }
    }
}

