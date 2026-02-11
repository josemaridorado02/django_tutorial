pipeline {
    environment {
        DOCKER_IMAGE = "josemaridorado02/django-polls"
        DOCKER_CREDENTIALS = 'dockerhub-credentials'
    }
    
    agent none
    
    stages {
        stage('Test') {
            agent {
                docker {
                    image 'python:3.12-slim'
                    args '-u root:root'
                }
            }
            stages {
                stage('Install') {
                    steps {
                        sh 'pip install -r requirements.txt'
                    }
                }
                stage('Test Django') {
                    steps {
                        sh 'python3 manage.py test'
                    }
                }
            }
        }
        
        stage('Build and Push') {
            agent any
            stages {
                stage('Build Docker Image') {
                    steps {
                        script {
                            sh "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."
                        }
                    }
                }
                stage('Push to Docker Hub') {
                    steps {
                        script {
                            docker.withRegistry('', DOCKER_CREDENTIALS) {
                                sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                            }
                        }
                    }
                }
                stage('Clean Up') {
                    steps {
                        sh "docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                    }
                }
            }
        }
    }
    
    post {
        always {
            mail to: 'josemariaoteodorado12@gmail.com',
            subject: "Pipeline: ${currentBuild.fullDisplayName}",
            body: "Build ${env.BUILD_URL} - Result: ${currentBuild.result}"
        }
    }

stage('Deploy to VPS') {
    agent any
    steps {
        script {
            sh """
                ssh -o StrictHostKeyChecking=no oteo@jotedor.com 'bash ~/django-polls/deploy.sh'
            """
        }
    }
}
