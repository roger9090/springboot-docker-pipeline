pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'yourdockerhubusername/springboot-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://your-git-repo-url'
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

        stage('SonarQube Analysis') {
            environment {
                SONARQUBE = credentials('sonarqube-token')
            }
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh './mvnw sonar:sonar'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 8080:8080 $DOCKER_IMAGE'
            }
        }
    }
}

