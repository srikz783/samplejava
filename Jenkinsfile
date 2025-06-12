pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/srikz783/samplejava/.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
