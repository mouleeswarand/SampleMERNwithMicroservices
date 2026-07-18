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

        stage('Login to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'mouli-aws-creds'
                ]]) {
                    sh '''
                    aws ecr-public get-login-password --region us-east-1 | \
                    docker login --username AWS --password-stdin public.ecr.aws
                    '''
                }
            }
        }

        stage('Tag Images') {
            steps {
                sh '''
                docker tag frontend:latest public.ecr.aws/b5a0w1i5/mernservices:frontend
                docker tag hello:latest public.ecr.aws/b5a0w1i5/mernservices:hello
                docker tag profile:latest public.ecr.aws/b5a0w1i5/mernservices:profile
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker push public.ecr.aws/b5a0w1i5/mernservices:frontend
                docker push public.ecr.aws/b5a0w1i5/mernservices:hello
                docker push public.ecr.aws/b5a0w1i5/mernservices:profile
                '''
            }
        }

    }
}