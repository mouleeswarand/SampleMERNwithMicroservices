pipeline {
    agent any

    stages {

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t frontend:latest .'
                }
            }
        }

    }
}