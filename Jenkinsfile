pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('siddhesh261-dockerhub')
    }
    stages {
        stage('Build image') {
            steps {
                sh 'docker build -t siddhesh261/flaskapp:$BUILD_NUMBER .'
            }
        }
        stage('Test image') {
            steps {
                echo 'Empty'
            }
        }
        stage('Connecting to DockerHub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Push image') {
            steps {
                sh 'docker push siddhesh261/flaskapp:$BUILD_NUMBER'
            }
        }
    }
    post {
        always {
            script {
                sh 'docker logout'
            }
        }
    }
}
