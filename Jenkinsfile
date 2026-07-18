pipeline {
    agent any

    stages {

        stage('Check Environment') {
            steps {
                sh 'pwd'
                sh 'ls -la'
                sh 'docker --version'
                sh 'aws --version'
                sh 'git --version'
            }
        }

    }
}