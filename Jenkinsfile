pipeline { 
    agent any
    
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }

    environment {
        IMAGE_NAME = 'srikanth/my-docker-app'
        TAG = "${env.BUILD_NUMBER}"
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
                sh 'mvn test -DskipTest=true'
                //sh 'mvn test'
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

        stage('Trivy FS scan') {
            steps {
                sh "trivy fs --format table -o result.txt ."
            }
        }
        
        stage('Package') {
            steps {
                sh "mvn package"
            }
        }
        stage('publish') {
            steps {
                script {
                    withMaven(globalMavenSettingsConfig: '', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: 'maven.settings', traceability: true) {
                        sh "mvn clean deploy"
                    }
                }
            }
        }
       
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${TAG} ."
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script {
                    // Stop if already running
                    sh """
                    docker rm -f $CONTAINER_NAME || true
                    docker run -d -p 8082:8080 --name $CONTAINER_NAME ${IMAGE_NAME}:${TAG}
                    """
                }
            }
        }
        stage('Docker push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-login') {
                sh "docker push ${IMAGE_NAME}:${TAG}"
                    }
                }
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
