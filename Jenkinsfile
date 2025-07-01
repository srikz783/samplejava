pipeline { 
    agent any
    
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }

    environment {
        IMAGE_NAME = 'my-docker-app'
        CONTAINER_NAME = 'my-app-container'
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        
        stage('Compilee') {
            steps {
            sh  "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                //sh 'mvn clean install -DskipTest'
                sh 'mvn test'
            }
        }
        stage('sonarqube') {
            steps {
               withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=bloggingApp -Dsonar.projectKey=bloggingApp \
                            -Dsonar.java.binaries=target '''
                   sh "echo $SCANNER_HOME"
                }
            }
        }
        
        stage('Package') {
            steps {
                sh "mvn package"
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
                    docker run -d -p 8082:8080 --name $CONTAINER_NAME $IMAGE_NAME
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
