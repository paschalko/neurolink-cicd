pipeline {
    agent any

    options {
        timeout(time: 15, unit: 'MINUTES')
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '20'))
    }

    // Database password comes from the Jenkins credentials store (secret text).
    // Compose needs it for every command, so it is set for the whole pipeline.
    environment {
        POSTGRES_PASSWORD = credentials('neurolink-db-password')
    }

    // Runs as soon as GitHub sends a push webhook (needs the GitHub plugin
    // and a webhook pointing at https://<jenkins-host>/github-webhook/)
    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

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
                // Retry for up to 60 seconds instead of a fixed sleep
                retry(6) {
                    sleep(time: 10, unit: 'SECONDS')
                    // Checks from inside the app container, so it works whether
                    // Jenkins runs on the host or in its own container
                    sh '''docker exec neurolink_app python -c "import urllib.request; urllib.request.urlopen('http://localhost:5000/signup', timeout=5)"'''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful.'
        }
        failure {
            echo 'Pipeline failed. Check the stage logs above.'
        }
        always {
            sh 'docker image prune -f || true'
        }
    }
}
