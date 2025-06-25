pipeline { 
    agent any
    
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }

    environment {
        IMAGE_NAME = 'my-docker-app'
        CONTAINER_NAME = 'my-app-container'
    }

    stages {
        
        stage('Compilee') {
            steps {
            sh  "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }
        
        stage('Package') {
            steps {
                sh "mvn package"
            }
        }
        stage('Check Docker Version') {
            steps {
                sh 'docker --version'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $IMAGE_NAME ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Stop if already running
                    sh """
                    docker rm -f $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME $IMAGE_NAME
                    """
                }
            }
        }

        stage('Verify Running Container') {
            steps {
                sh "docker ps | grep $CONTAINER_NAME"
            }
        }
    }
    post {
        always {
            // Archive the build artifacts
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
        }
    }
}
