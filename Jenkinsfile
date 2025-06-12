pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
