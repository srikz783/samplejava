pipeline { 
    agent any
    
    tools {
        maven 'maven3'
        jdk 'jdk17'
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
    }
    post {
        always {
            // Archive the build artifacts
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
        }
    }
}
