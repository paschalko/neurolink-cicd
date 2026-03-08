pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    stages {
        stage('Build') {
            steps {
                sh 'docker-compose build neurolink'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker-compose up -d --no-deps neurolink'
            }
        }

        stage('Health Check') {
            steps {
                script {
                    sleep(10)
                    sh 'curl -f http://neurolink_app:5000/signup || curl -f http://172.17.0.1:5000/signup || exit 1'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Pipeline failed! Check the logs above.'
        }
    }
}