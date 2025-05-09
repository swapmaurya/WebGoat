pipeline {
    agent any

    environment {
        TARGET_HOST = 'www.webgoat.local'
        WEBGOAT_URL = "http://${TARGET_HOST}:8080/WebGoat"
    }

    stages {
        stage('Install WebGoat') {
            steps {
                sh '''
                    cd WebGoat
                    ./mvnw clean install
                '''
            }
        }

        stage('Print') {
            steps {
                sh '''
                    echo "swaptest"
                    ls
                '''
            }
        }

    }

    post {
        always {
            echo 'Cleaning up Docker containers...'
        }
    }
}
