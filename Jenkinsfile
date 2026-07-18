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

        stage('Build Hello Service') {
            steps {
                dir('backend/helloService') {
                    sh 'docker build -t hello:latest .'
                }
            }
        }

        stage('Build Profile Service') {
            steps {
                dir('backend/profileService') {
                    sh 'docker build -t profile:latest .'
                }
            }
        }

        stage('List Images') {
            steps {
                sh 'docker images'
            }
        }

    }
}